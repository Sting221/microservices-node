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
                          
                          echo password "$PASSWORD" | docker login "$harbor_url" -u "$USERNAME" --password-stdin
                          '''
                      }
            }
        }

        stage ("Tag") {
            steps{
                sh '''
                docker tag test1:v1 harbor.local/test-microservice/test1:v1
                docker tag test2:v1 harbor.local/test-microservice/test2:v1
                docker tag test3:v1 harbor.local/test-microservice/test3:v1
                docker tag test4:v1 harbor.local/test-microservice/test4:v1
                '''
            }
        }

        stage ("Push") {
            steps {
                sh '''
                docker push harbor.local/test-microservice/test1:v1
                docker push harbor.local/test-microservice/test2:v1
                docker push harbor.local/test-microservice/test3:v1
                docker push harbor.local/test-microservice/test4:v1
                '''
            }
        }
    }
                    
}
