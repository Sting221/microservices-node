pipeline{
    agent any

    tools{
        nodejs 'Node20'
    }

    environment {
        harbor_url = "harbor.local"
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
                docker build -t test2:v1 ./search
                docker build -t test3:v1 ./videos
                docker build -t test4:v1 ./web
                '''
            }
        }

        stage("image") {
            steps {
                sh 'docker images'
            }
        }

        stage("Harbor Login") {
            steps {
                withCredentials([usernamePassword(
                      credentialsId: 'harbor-cred',
                      usernameVariable: 'USERNAME',
                      passwordVariable: 'PASSWORD')]) {
                          
                          sh '''
                          
                          echo "$PASSWORD" | docker login "$horbor_url" -u "$USERNAME" --password-stdin
                          '''
                      }
            }
        }
    }
                    
}
