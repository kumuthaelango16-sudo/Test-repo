The failure occurred because the Kubernetes pod anti-affinity rules applied to the CloudBees controller and CJOC pods forced them to run across 18 out of 19 available cluster nodes. Due to this configuration, dynamic worker pods were only allowed to run on the single remaining node. When multiple pipelines triggered dynamic agents, this one node quickly reached its CPU and memory limits. As a result, Kubernetes was unable to schedule new dynamic worker pods and started terminating existing ones. This caused Jenkins to lose connection with the agents, leading to pipeline failures.

In summary, dynamic worker pod scheduling was unintentionally restricted to one node, resulting in resource exhaustion and agent failure under load



Reproduction and Testing in Non-Prod

To validate the problem and verify the solution, the issue was first recreated in the non-production CloudBees CI environment. The available worker nodes were intentionally limited to mimic the production setup where only one node was available for dynamic agents. Under this configuration, multiple pipeline jobs were triggered. The same agent failures and pod scheduling issues were observed, confirming that the problem was caused by node restriction and resource exhaustion.

Once the issue was reproduced, a dedicated worker node group was configured in non-prod. The CloudBees dynamic pod template was updated to point to this new node pool, ensuring worker pods were scheduled on dedicated nodes instead of sharing nodes with controllers. Load testing was then performed by triggering high-volume builds to simulate real pipeline activity. Dynamic pods were created successfully, queued correctly when capacity was full, and no failures or disconnections occurred.

This testing confirmed that assigning a separate node group for dynamic workers provides reliable pod scheduling and prevents resource contention, fully resolving the issue in a controlled non-production environment.

Work Done & Findings

After identifying the issue, the behavior was reproduced in the non-production CloudBees environment by simulating a limited-node scenario. As expected, dynamic worker pods failed when multiple pipelines were triggered, confirming that the issue was due to node restriction and resource saturation.

To address this, a dedicated worker node group was created in the non-prod cluster, and the dynamic pod template was updated to schedule pods only on this new worker pool. Controller pods continued using their existing nodes. Multiple test builds were then executed, including high-concurrency runs (100+ jobs). The dynamic workers were scheduled successfully, queued properly when capacity was full, and no pod failures or agent disconnects were observed.

These tests validated that separating controller nodes from dynamic worker nodes resolves the issue and ensures stable scaling of pipeline workloads.

The recommended solution is to introduce a dedicated worker node group for dynamic agents in the production TKGI cluster. This ensures that dynamic worker pods are scheduled on separate nodes, preventing any resource conflict with controller and CJOC pods. By isolating worker nodes from controller nodes, we restore capacity for dynamic scaling and avoid bottlenecks caused by affinity restrictions.

To implement this solution, we will first create and configure the dedicated worker node pool in the production cluster. The CloudBees controller configuration will continue using the existing nodes, and the dynamic pod template will be updated to point to the new worker pool through appropriate node selectors or labels. After applying the configuration, we will trigger pipeline workloads to confirm that dynamic agents launch correctly and scale as expected.

Once deployed, the environment will be closely monitored to ensure stable scheduling, agent connectivity, and resource utilization. This rollout approach minimizes risk and ensures a smooth transition from the temporary static worker setup. The plan ensures that CloudBees pipelines can reliably scale without encountering resource contention or node availability constraints.

