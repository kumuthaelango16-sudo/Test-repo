Yesterday, we worked with Asharaf until 3 AM on generating failure scenarios. Even after setting CPU to 2 and memory requests/limits to 4Gi, the pod did not utilize full capacity, so we could not reproduce the failure. We identified an alternative approach to trigger full-capacity load on the pod.
Asharaf has also requested testing of additional scenarios, for which evidence collection is required from TKGI worker nodes. Since our platform team does not have node access, TKGI team support is needed. Asharaf will schedule a call with the TKGI team to proceed.

Option 2: Concise Daily Standup Style

Worked with Asharaf till 3 AM on failure scenario testing.

Could not reproduce the failure as the pod didn’t utilize the full CPU/memory despite updated limits.

Found another method to generate full-capacity pod load.

Additional scenarios identified; need TKGI team for node-level evidence (platform team has no worker node access).

Asharaf will set up a call with the TKGI team.

Option 3: Email/Slack Ready

Update:
We attempted failure scenario testing with Asharaf late last night, but the pod did not consume full CPU/memory even after setting 2 CPU and 4Gi limits, so the issue couldn’t be reproduced. We have identified an alternative method to push the pod to full capacity.
Asharaf has requested testing of a few more scenarios, which require evidence from the worker nodes. Since the platform team doesn’t have node access, TKGI team support is needed. Asharaf will coordinate and schedule a call with TKGI to continue the tests.
