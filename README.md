def runStageWithRetry(stageName, closure) {
    int maxRetries = 1

    for (int attempt = 0; attempt <= maxRetries; attempt++) {
        try {
            stage(stageName) {
                closure()
            }
            return // success → exit

        } catch (err) {
            def log = currentBuild.rawBuild.getLog(2000).join("\n").toLowerCase()

            if (isOOM(log) && attempt < maxRetries) {
                echo "OOM detected in stage: ${stageName}"

                // Prevent infinite retry
                if (params.AGENT_LABEL_OVERRIDE == 'dynamic_node_14Gi') {
                    echo "Already retried with high memory. Skipping retry."
                    throw err
                }

                // Override agent label for retry
                env.AGENT_LABEL = 'dynamic_node_14Gi'
                echo "Retrying ${stageName} on higher memory node..."

            } else {
                throw err
            }
        }
    }
}

def runStageWithRetry(stageName, closure) {
    int maxRetries = 1

    for (int attempt = 0; attempt <= maxRetries; attempt++) {
        try {
            stage(stageName) {
                closure()
            }
            return // success → exit

        } catch (err) {
            def log = currentBuild.rawBuild.getLog(2000).join("\n").toLowerCase()

            if (isOOM(log) && attempt < maxRetries) {
                echo "OOM detected in stage: ${stageName}"

                // Prevent infinite retry
                if (params.AGENT_LABEL_OVERRIDE == 'dynamic_node_14Gi') {
                    echo "Already retried with high memory. Skipping retry."
                    throw err
                }

                // Override agent label for retry
                env.AGENT_LABEL = 'dynamic_node_14Gi'
                echo "Retrying ${stageName} on higher memory node..."

            } else {
                throw err
            }
        }
    }
}

pipeline {
    agent none

    parameters {
        string(name: 'AGENT_LABEL_OVERRIDE', defaultValue: '', description: '')
    }

    stages {

        stage('Build') {
            agent { label params.AGENT_LABEL_OVERRIDE ?: 'dynamic_node' }
            steps {
                script {
                    runStageWithRetry('Build') {
                        sh 'echo Building...'
                        // sh 'exit 137' // simulate OOM
                    }
                }
            }
        }

        stage('Test') {
            agent { label params.AGENT_LABEL_OVERRIDE ?: env.AGENT_LABEL ?: 'dynamic_node' }
            steps {
                script {
                    runStageWithRetry('Test') {
                        sh 'echo Testing...'
                    }
                }
            }
        }

        stage('Deploy') {
            agent { label params.AGENT_LABEL_OVERRIDE ?: env.AGENT_LABEL ?: 'dynamic_node' }
            steps {
                script {
                    runStageWithRetry('Deploy') {
                        sh 'echo Deploying...'
                    }
                }
            }
        }
    }
}
