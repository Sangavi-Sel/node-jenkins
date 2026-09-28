pipeline {
    agent any
    environment{
        IMAGE = 'sangavi17/node_dynamic'
        CONTAINER = 'static-container'
    }
    stages{
        stage('checkout'){
            steps {
                checkout scm
            }

        }

        stage('Build Docker Image'){
            steps{
                sh '''
                    docker build -t ${IMAGE}:v${BUILD_NUMBER} .
                '''
            }
        }

        stage('Docker login'){
            steps{
                withCredentials([
                    usernamePassword(
                        credentialsId: 'node',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]

                )
                {
                sh '''
                echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
             
                '''
                }
            }
        }

        stage('push to dockerhub'){
            steps{
                sh '''
                docker push ${IMAGE}:v${BUILD_NUMBER}
                '''
            }
        }

        stage('Remove Local Image') {
            steps {
                sh '''
                    docker rmi ${IMAGE}:v${BUILD_NUMBER} || true
                    docker rmi ${IMAGE}:latest || true
                '''
            }
        }

          stage('Pull from Docker Hub') {
            steps {
                sh '''
                    docker pull ${IMAGE}:v${BUILD_NUMBER}
                '''
            }
        }

        stage('Run Docker Container'){
            steps{
                sh '''
                    docker rm -f ${CONTAINER} || true
                    docker run -d --name ${CONTAINER} -p 80:80 ${IMAGE}:v${BUILD_NUMBER}
                '''
            }
        }

        stage('Verify'){
            steps{
                sh '''
                    docker ps
                    docker images ${IMAGE}
                '''
            }
        }
    }
}