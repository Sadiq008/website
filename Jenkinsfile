pipeline {
    agent any
    
    environment {
        // Define environment variables if needed
        DOCKER_REGISTRY = 'your-docker-registry-url'
        DOCKER_IMAGE_NAME = 'your-docker-username/website'
        KUBE_CONFIG = credentials('your-kube-config-credentials-id')
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout code from the repository
                checkout scm
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    // Build Docker image
                    docker.build("${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}")
                    
                    // Push Docker image to Docker Hub
                    docker.withRegistry('https://${DOCKER_REGISTRY}', 'docker-hub-credentials-id') {
                        docker.image("${DOCKER_IMAGE_NAME}:latest").push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy to Kubernetes using kubectl
                    def kubeconfig = readCredentials("${KUBE_CONFIG}")
                    sh "kubectl --kubeconfig=${kubeconfig} apply -f deployment.yaml"
                }
            }
        }
    }
}

def readCredentials(credentialsId) {
    withCredentials([string(credentialsId: credentialsId, variable: 'KUBE_CONFIG')]) {
        return sh(script: "echo \$KUBE_CONFIG", returnStdout: true).trim()
    }
}
