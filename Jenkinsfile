pipeline {
    agent any
    
    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials-id'
        DOCKER_IMAGE = 'living9host/flask-app'
        CONTAINER_NAME = 'flask-container'
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

                    // Run the new container
                    bat """
                    docker run -d --name ${env.CONTAINER_NAME} -p 5000:5000 ${DOCKER_IMAGE}:${env.BUILD_ID}
                    """
                }
            }
        }
    }
}