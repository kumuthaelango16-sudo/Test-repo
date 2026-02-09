pipeline {
  agent any

  environment {
    SONAR_URL   = 'https://sonar.company.com'
    SONAR_TOKEN = credentials('sonar-token')
  }

  parameters {
    string(name: 'PROJECTS', description: 'Comma separated project keys')
    string(name: 'METRICS',  description: 'Comma separated metrics')
    choice(name: 'PERIOD_TYPE', choices: ['quarterly','monthly','custom'])
    string(name: 'FROM_DATE', defaultValue: '')
    string(name: 'TO_DATE', defaultValue: '')
  }

  stages {

    stage('Prepare Dates') {
      steps {
        sh '''
          chmod +x scripts/date_calculator.sh
          ./scripts/date_calculator.sh "$PERIOD_TYPE" "$FROM_DATE" "$TO_DATE"
        '''
      }
    }

    stage('Parallel Fetch') {
      steps {
        script {
          def branches = [:]
          params.PROJECTS.split(',').each { p ->
            branches[p.trim()] = {
              retry(3) {
                sh """
                  chmod +x scripts/fetch_sonar_data.sh
                  ./scripts/fetch_sonar_data.sh \
                    "${p.trim()}" \
                    "${params.METRICS}" \
                    "$SONAR_URL" \
                    "$SONAR_TOKEN"
                """
              }
            }
          }
          parallel branches
        }
      }
    }

    stage('Merge Reports') {
      steps {
        sh '''
          chmod +x scripts/merge_reports.sh
          ./scripts/merge_reports.sh
        '''
      }
    }

    stage('Generate Excel') {
      steps {
        sh 'cp master.csv master.xlsx'
      }
    }

    stage('Trend Graphs') {
      steps {
        sh 'python3 scripts/generate_trend.py master.csv'
      }
    }

    stage('Push Dashboard') {
      steps {
        sh '''
          chmod +x scripts/push_dashboard.sh
          ./scripts/push_dashboard.sh master.csv
        '''
      }
    }
  }

  post {
    success {
      emailext(
        subject: "Sonar & Copilot Adoption Report",
        body: "Attached latest reports.",
        attachmentsPattern: "*.csv,*.xlsx,*.png",
        to: "team@company.com"
      )
    }

    always {
      archiveArtifacts artifacts: '*.csv,*.xlsx,*.png'
    }
  }
}
