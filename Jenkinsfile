pipeline {

    agent any

    environment {
        IMAGE_NAME = "Supriya-30/backend"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                dir('backend') {
                    sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
                }
            }
        }

        stage('Trivy Security Scan') {
            steps {
                sh 'trivy image $IMAGE_NAME:$IMAGE_TAG'
            }
        }

        stage('DockerHub Login') {
            steps {

                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh '''
                    echo $DOCKER_PASS | docker login \
                    -u $DOCKER_USER --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
            }
        }

        stage('Update Kubernetes Deployment') {
            steps {

                sh '''
                kubectl set image deployment/backend \
                backend=$IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }
    }
}
