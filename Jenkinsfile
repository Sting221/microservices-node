pipeline{
    agent any

    tools{
        nodejs 'Node20'
    }

    stages{
        stage('checkout'){
            steps{
                echo 'checking the repo'
                checkout scm
            }
        }

        stage('SonarQube analysis') {
            steps {
                script {
                    def scannerHome = tool 'sonarqube'
                    withSonarQubeEnv('sonar-dummy')  {
                      sh "${scannerHome}/bin/sonar-scanner
                       }
                    }
                }
            }
        }

        stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
              }
            }
        }
    }
                    
}
