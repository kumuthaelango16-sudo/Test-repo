def isOOM(log) {
    def oomPattern = ~/(outofmemoryerror|java\s+heap\s+space|gc\s+overhead\s+limit\s+exceeded|oomkilled|killed\s+process|exit\s+code\s+137|container\s+killed)/
    return oomPattern.matcher(log).find()
}


def handleMemoryIssue() {
    echo "Memory issue detected, retrying build..."

    // جلوگیری infinite loop
    if (params.AGENT_LABEL_OVERRIDE == 'dynamic_node_14Gi') {
        echo "Already retried with higher memory node. Skipping retry."
        return
    }

    // Collect all parameters and override only one
    def newParams = params.collect { key, value ->
        if (key == 'AGENT_LABEL_OVERRIDE') {
            return string(name: key, value: 'dynamic_node_14Gi')
        } else {
            return string(name: key, value: value.toString())
        }
    }

    echo "Triggering rebuild with updated agent label..."

    build job: env.JOB_NAME,
          parameters: newParams,
          wait: false
}
