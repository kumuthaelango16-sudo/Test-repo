
def SonarScan = "No"
def NexusIQScan = "No"
def FortifyScan = "No"
def ManifestValidation = [:]
def failedStages = []

def devopsPipelineFlow = (params.DEVOPS_PIPELINE_FLOW != null) ?
    params.DEVOPS_PIPELINE_FLOW :
    "Application-Build-and-Deploy-Flow"


/* ----------------------------------------------------
   Self Healing Memory Retry Logic
   1st Try  : env.AGENT_LABEL OR dynamic_node (4Gi)
   2nd Try  : dynamic_node_8Gi
   3rd Try  : dynamic_node_14Gi
---------------------------------------------------- */

def runWithMemoryFallback(String stageName, Closure body) {

    def labels = [
        env.AGENT_LABEL ?: "dynamic_node",
        "dynamic_node_8Gi",
        "dynamic_node_14Gi"
    ]

    for (int i = 0; i < labels.size(); i++) {

        try {

            node(labels[i]) {

                echo "Running ${stageName} on ${labels[i]}"
                body()
            }

            return
        }
        catch (Exception e) {

            echo "${stageName} failed on ${labels[i]}"

            if (isOOMError(e)) {

                if (i == labels.size() - 1) {
                    failedStages << stageName
                    error("${stageName} failed due to OOM even on 14Gi")
                }

                echo "OOM detected. Retrying ${stageName} on higher memory node..."
            }
            else {
                failedStages << stageName
                throw e
            }
        }
    }
}

def isOOMError(def err) {

    def msg = err.toString().toLowerCase()

    return msg.contains("oom") ||
           msg.contains("outofmemory") ||
           msg.contains("java heap space") ||
           msg.contains("exit code 137") ||
           msg.contains("killed") ||
           msg.contains("container terminated")
}



pipeline {

    agent none

    options {
        timeout(activity: true, time: 210, unit: 'MINUTES')
    }

    environment {
        TOOL_NAME = "Maven"
    }

    stages {

        stage('Workspace Cleanup') {
            steps {
                script {
                    runWithMemoryFallback("Workspace Cleanup") {
                        cleanWs deleteDirs: true
                    }
                }
            }
        }

        stage('PreValidation Check') {
            steps {
                script {
                    runWithMemoryFallback("PreValidation Check") {
                        execValidationChecks(params)
                    }
                }
            }
        }

        stage('ScanParallel') {

            when {
                expression {
                    devopsPipelineFlow.contains("Application-Build-and-Deploy-Flow")
                }
            }

            parallel {

                stage('Sonar') {
                    steps {
                        script {
                            runWithMemoryFallback("Sonar") {
                                env.TOOL_NAME = "SonarQube"
                                SonarScan = execSonarScan(params)
                            }
                        }
                    }
                }

                stage('Fortify') {
                    steps {
                        script {
                            runWithMemoryFallback("Fortify") {
                                env.TOOL_NAME = "Fortify"
                                FortifyScan = execFortifyScan(params)
                            }
                        }
                    }
                }

                stage('Nexus-IQ') {
                    steps {
                        script {
                            runWithMemoryFallback("Nexus-IQ") {
                                env.TOOL_NAME = "NexusIQ"
                                NexusIQScan = execNexusIQScan(params)
                            }
                        }
                    }
                }
            }
        }

        stage('Jmeter Performance Test') {

            when {
                expression {
                    params.RUNPERFORMANCETEST == "Yes"
                }
            }

            steps {
                script {
                    runWithMemoryFallback("Jmeter Performance Test") {
                        execJMeter(params)
                    }
                }
            }
        }

        stage('Newman') {

            when {
                expression {
                    devopsPipelineFlow.contains("Application-Build-and-Deploy-Flow")
                }
            }

            steps {
                script {
                    runWithMemoryFallback("Newman") {
                        execNewMan(params)
                    }
                }
            }
        }

    }

    post {

        always {

            script {

                if (params.RUNPERFORMANCETEST == "Yes") {

                    perfReport(
                        filterRegex: '',
                        showTrendGraphs: true,
                        sourceDataFiles: '**/*.jtl'
                    )
                }

                execEmail(params)

                if (currentBuild.result == 'SUCCESS') {

                    buildSuccess()

                } else if (currentBuild.result == 'UNSTABLE') {

                    buildFailed("Build is Unstable", "UNSTABLE")

                } else if (currentBuild.result == 'ABORTED') {

                    buildAborted()

                } else {

                    def failedStageMessage = failedStages.join(", ")

                    buildFailed(
                        "Build is Failing at stage ${failedStageMessage}",
                        "FAILURE"
                    )
                }
            }
        }
    }
}
