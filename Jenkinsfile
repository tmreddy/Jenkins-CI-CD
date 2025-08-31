pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "tmreddy/my-node-app"
        DOCKER_TAG = "latest"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/tmreddy/Jenkins-CI-CD'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t $DOCKER_IMAGE:$DOCKER_TAG ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                        sh "docker push $DOCKER_IMAGE:$DOCKER_TAG"
                    }
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    sh """
                    docker stop my-node-app || true
                    docker rm my-node-app || true
                    docker run -d --name my-node-app -p 3000:3000 $DOCKER_IMAGE:$DOCKER_TAG
                    """
                }
            }
        }
    }
}
