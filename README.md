CloudBees CI Dynamic Worker Node Failure – RCA & Solution Plan
Summary

On 02-Sep-2025, CloudBees CI pipelines using dynamic Kubernetes workers failed during execution. Investigation revealed that Kubernetes pod anti-affinity rules limited dynamic worker pods to only one node in the TKGI cluster. When pipeline load increased, this node ran out of resources, causing pod failures and pipeline agent disconnections.

To address this, a dedicated node group for dynamic workers was created and tested in the non-prod environment. The tests were successful, and the same approach will be implemented in production.

Issue Description

The CloudBees deployment has 19 worker nodes. Due to pod anti-affinity settings applied to CloudBees Controller and CJOC pods, these pods were forced to spread across 18 nodes, leaving only one node available for dynamic worker pods.

During high build activity, multiple pipelines attempted to spin up dynamic agents, which caused:

Node resource saturation (CPU & memory)

Dynamic worker pod eviction/failure

Jenkins losing connection to agents

Pipeline failures and increased queue times

Typical Error Messages

Cannot contact globalpodtemplate...
runningAgent deleted; node was terminated
Could not connect to dynamic pod due to resource failure

Root Cause

Anti-affinity rules forced controllers across 18 nodes

Dynamic workers restricted to one node

Node reached resource limit under load

Kubernetes evicted or blocked worker pods

Jenkins pipelines failed due to missing agents

Reproduction & Testing in Non-Prod

To confirm the issue and test the solution:

Simulated reduced node availability in non-prod

Observed the same worker pod failures

Created a separate node group for dynamic agents

Updated dynamic pod templates to use new node group

Triggered 100+ dynamic jobs for load validation

Test Results

Dynamic pods distributed across new node group

No agent eviction or startup failures

Excess pods queued normally

Pipelines executed without failure

Temporary fallback to static worker nodes was used in production until validation was complete.

Proposed Solution

Move to dedicated worker node group for dynamic agents in production.

This ensures:

Controller pods remain isolated and stable

Dynamic agents scale independently during peak load

No single-node scheduling bottleneck

Improved reliability and CI throughput

Additionally, resource requests/limits and monitoring will be enabled to avoid future saturation.

Implementation Plan

Steps:

Create dedicated worker pool in production cluster

Update CloudBees controller node selector config

Modify dynamic pod template to use new pool label

Validate scheduling and agent connectivity

Monitor usage, load behavior, and build queues

Conclusion

The pipeline failures were due to anti-affinity constraints limiting dynamic pods to one node. The dedicated worker node strategy has been thoroughly tested in non-prod and proven effective. Implementing this in production will restore stability and support scalable dynamic pipeline execution.

Next Steps

Roll out the dedicated worker node configuration to production

Monitor cluster and pipeline performance post-cutover

Move workload gradually and keep static worker option as fallback for initial period
