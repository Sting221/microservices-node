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
                    withSonarQubeEnv('sonar-dummy') {
                        dir('microservices'){// If you have configured more than one global server connection, you can specify its name as configured in Jenkins
                            sh "${scannerHome}/bin/sonar-scanner"
                        }
                    }
                }
            }
        }

        stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'MINUTE') {
                waitForQualityGate abortPipeline: true
              }
            }
        }
    }
                    
}
