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
                      sh "${scannerHome}/bin/sonar-scanner"
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

        stage("build") {
            steps {
                sh '''
                docker build -t test1:v1 ./books
                docker build -t test2:v1 ./images
                docker build -t test3:v1 ./search
                docker build -t test4:v1 ./videos
                docker build -t test5:v1 ./web
                '''
            }
        }

        stage("image") {
            steps {
                sh 'docker images'
            }
        }
    }
                    
}
