pipeline {
    agent {
        label 'node1'
    }
    
    environment {
        // Define environment variables
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
        DOCKER_IMAGE_NAME = 'abbadi998/newapp'
    }
    
    stages {
        stage('Checkout') {
            steps {
                // Checkout code from GitHub
                git branch: 'master', url: 'https://github.com/abbadiomer9/DevOpsClassCodes.git'
            }
        }

        stage('Build') {
            steps {
                script {
                    // Build the code using Maven
                    sh 'mvn clean'
                    sh 'mvn clean package -Dmaven.war.plugin.version=3.3.2'
                    sh 'mvn help:effective-pom'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    def imageTag = "${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}"
                    sh "docker build -t ${imageTag} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Log in to Docker Hub
                    withDockerRegistry([credentialsId: "${DOCKER_CREDENTIALS_ID}", url: 'https://index.docker.io/v1/']) {
                        def imageTag = "${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}"
                        // Push the Docker image to Docker Hub
                        sh "docker push ${imageTag}"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Set the image tag in the deployment file
                    def imageTag = "${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}"
                    sh "kubectl run newpod24 --image ${imageTag}"
                    sh "kubectl expose pod newpod24 --port=8080 --target-port=8080 --name=my-service24 --type=NodePort"
                   }
            }
        }
    }
    
    post {
        always {
            cleanWs() // Clean workspace after the build
        }
    }
}
