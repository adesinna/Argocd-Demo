pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = "adesinna"
        APP_NAME = "flask-application"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}/${APP_NAME}"
        REGISTRY_CREDS = 'dockerhub'
    }

    stages {
        stage('Clean up workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout SCM') {
            steps {
                // Use your GitHub credentials here
                git credentialsId: 'your-github-credentials', url: 'https://github.com/adesinna/argo-cd.git', branch: 'dev'
            }
        }

        stage('Build Image') {
            steps {
                script {
                    // Build the Docker image with the specified tag
                    def docker_image = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }

        stage('Push Image') {
            steps {
                script {
                    // Push the image to Docker Hub with both the build number and 'latest' tags
                    docker.withRegistry('', REGISTRY_CREDS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }
        }

        stage('Remove Images') {
            steps {
                // Remove the local Docker images (use --force to force removal)
                sh "docker rmi -f ${IMAGE_NAME}:${IMAGE_TAG}"
                sh "docker rmi -f ${IMAGE_NAME}:latest"
            }
        }
    }
}
