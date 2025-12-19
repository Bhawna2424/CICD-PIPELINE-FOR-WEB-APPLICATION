pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "ramansingh1410/beauty-website"
        TAG = "latest"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-id-pass',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                      echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Build Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$TAG .'
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $DOCKER_IMAGE:$TAG'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                  docker pull $DOCKER_IMAGE:$TAG
                  docker stop beauty || true
                  docker rm beauty || true
                  docker run -d -p 80:80 --name beauty $DOCKER_IMAGE:$TAG
                '''
            }
        }
    }
}
