pipeline {
    agent any
    
    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials-id'
        DOCKER_IMAGE = 'living9host/flask-app'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/your-username/your-flask-repo.git'
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
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy Docker Container') {
            steps {
                sh 'docker run -d -p 5000:5000 ${DOCKER_IMAGE}:${env.BUILD_ID}'
            }
        }
    }
}
