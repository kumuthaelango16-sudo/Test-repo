Dynamic Worker Node Pipeline Failure – RCA & Solution Proposal
Summary

On September 02, 2025, CloudBees CI dynamic worker node–based pipelines started failing due to pod anti-affinity restrictions in the Kubernetes (TKGI) cluster. The dynamic worker pod scheduling was restricted to a single node, causing resource exhaustion and pipeline execution failures.

Background

CloudBees CI uses dynamic Kubernetes pods to execute Jenkins pipelines. The current TKGI cluster consists of 19 worker nodes, of which:

18 nodes are occupied by controller and CJOC pods due to pod anti-affinity rules

Only 1 node allowed scheduling of dynamic worker pods

This resulted in resource saturation on the single available node when pipelines triggered at scale.

Impact

Frequent build agent failures and pipeline execution interruptions

Queue backlog and slowdown in CI pipeline execution

Reduced CI system reliability for development teams

Increased time to complete builds and deployments

Temporary fallback to static worker nodes to maintain business continuity

Error Details
Cannot contact globalpodtemplate-6k06h: java.lang.InterruptedException2045843140-prod/globalpodtemplate-6k06h Pod just failed
.jnlp — runningAgent globalpodtemplate-6k06h was deleted; cancelling node body
Could not connect to globalpodtemplate-6k06h to send interrupt signal to process

Root Cause
Observation	Conclusion
Pod anti-affinity rules forced controller and CJOC pods across 18 nodes	Dynamic worker pods squeezed to one node
Only one node left for dynamic agents	Node reached resource limit
High concurrency pipeline triggered	Pod scheduling failures
Pod scheduling throttled	Build queue backlog, timeouts
Technical RCA

Pod anti-affinity rules currently applied to CloudBees controller and CJOC pods prevent dynamic agents from using those nodes

Dynamic agents attempted to launch only on the last unreserved node

Once node memory/CPU limits reached → K8s evicted worker pods

CloudBees failed to reconnect → pipeline failure

Steps Taken
Action	Result
Simulated reduced-node condition in non-prod	Successfully reproduced the issue
Tested new dedicated worker node group	Verified pod scheduling stability
Allocated lower resources to stress test	Agents failed as expected, confirming issue
Increased concurrency to 100	Agents queued properly; system handled expected load
Proposed Solution
✅ Create a dedicated worker node group for dynamic agents

Modify controller config to select existing node group only for controllers/CJOC

Configure dynamic pod templates to utilize new worker-only node group

Decouple controller nodes from worker pod scheduling

Benefits

Prevents resource contention between controllers and worker pods

Improves CI reliability and scalability

Ensures stable scheduling for high-concurrency jobs

Implementation Plan
Stage	Task	Status
Non-Prod Test	Create dedicated worker node pool	✅ Completed
Controller update to use existing nodes only	✅ Completed	
Dynamic agent pod template update	✅ Completed	
Load testing (100+ jobs)	✅ Stable	
Production rollout	Pending approval	
Current Workaround

Using static worker nodes temporarily in production until successful validation and approval for rollout.

Recommendations

Proceed with production rollout of dedicated node group

Define resource requests/limits for worker pods to avoid eviction

Set agent concurrency based on cluster sizing & business demand

Monitor CPU/Memory usage after implementation

Implement CloudBees pod autoscaling policies (future enhancement)

Conclusion

The pipeline failures were caused by Kubernetes anti-affinity settings, restricting dynamic worker pods to a single node.
A dedicated worker node strategy has been tested successfully in non-prod and is recommended for production deployment.

Appendix
Tools & Platforms

CloudBees CI

TKGI Kubernetes Cluster

Jenkins Dynamic Pod Template

Helm-based deployment

Key Labels
app=test-worker-node


Let me know if you'd like this delivered in:
