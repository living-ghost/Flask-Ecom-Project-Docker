pipeline {
    agent any
    
    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials-id'
        DOCKER_IMAGE = 'living9host/flask-app'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/living-ghost/Flask-Ecom-Project-Docker.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:${env.BUILD_ID}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', DOCKER_CREDENTIALS_ID) {
                        try {
                            dockerImage.push()
                            echo 'Image pushed successfully'
                        } catch (Exception e) {
                            echo "Failed to push image: ${e.getMessage()}"
                        }
                    }
                }
            }
        }

        stage('Deploy Docker Container') {
            steps {
                script {
                    // Stop and remove any existing container with the same name
                    sh """
                    if [ \$(docker ps -q -f name=flask-container) ]; then
                        docker stop flask-container
                        docker rm flask-container
                    fi
                    """

                    // Run the new container
                    sh """
                    docker run -d --name flask-container -p 5000:5000 ${DOCKER_IMAGE}:${env.BUILD_ID}
                    """
                }
            }
        }
    }
}