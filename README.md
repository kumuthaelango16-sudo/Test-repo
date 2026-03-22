pipeline {
    agent none

    stages {
        stage('Run Job') {
            agent {
                // Priority:
                // 1. Override from triggered build
                // 2. Global env variable
                // 3. Default fallback
                label "${params.AGENT_LABEL_OVERRIDE ?: (env.AGENT_LABEL ?: 'Dev_Common_Node')}"
            }

            steps {
                script {

                    // Resolve final agent (for logging)
                    def selectedAgent = params.AGENT_LABEL_OVERRIDE ?: (env.AGENT_LABEL ?: 'Dev_Common_Node')
                    echo "Running on agent: ${selectedAgent}"

                    try {
                        // 👉 Replace this with your actual job
                        sh '''
                        echo "Starting execution..."
                        # your real commands go here
                        sleep 5
                        '''

                    } catch (err) {

                        // 🔍 Detect OOM scenarios
                        def isOOM = err.toString().contains('OutOfMemoryError') ||
                                    err.toString().contains('Java heap space') ||
                                    err.toString().contains('Killed')

                        if (isOOM && !params.AGENT_LABEL_OVERRIDE) {
                            echo "OOM detected! Triggering new build on dynamic_node_8gi..."

                            build job: env.JOB_NAME,
                                  parameters: [
                                      string(name: 'AGENT_LABEL_OVERRIDE', value: 'dynamic_node_8gi')
                                  ],
                                  wait: false
                        }

                        // Fail current build
                        error("Build failed due to: ${err}")
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Build completed successfully."
        }
        failure {
            echo "Build failed. Check logs."
        }
    }
}
