Slide 1: Problem Statement

CloudBees CI pipelines using dynamic worker nodes failed during execution

Dynamic agents were unable to start or connect

Builds were stuck in queue or terminated unexpectedly

Issue impacted pipeline reliability and build completion

Slide 2: Root Cause

Pod anti-affinity settings forced controller and CJOC pods to occupy 18 of 19 cluster nodes

Only one node left available for dynamic worker pods

During high load, the single node reached CPU and memory limits

Kubernetes failed to schedule or evicted dynamic pods

Resulted in agent disconnects and pipeline failures

Slide 3: Reproduction & Testing (Non-Prod)

Simulated the same setup in non-production environment

Restricted nodes to reproduce the scheduling limitation

Dynamic agent failures observed as in production

Created dedicated worker node group and updated pod templates

Performed load testing with 100+ parallel jobs

Dynamic pods launched and queued successfully with no failures

Slide 4: Solution

Implement a dedicated node group for dynamic worker pods in production

Controller pods continue using existing nodes

Isolates dynamic agents → avoids resource contention

Enables stable scaling and reliable job execution

Continue using static worker nodes as temporary workaround until rollout
