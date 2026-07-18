
pipeline {
    agent any

    environment {
        DOCKER_USER = 'zeeshankanuga'
    }
    stages {
        stage('Checkout') {
            steps {
                // Checkout code from the repository
                git 'https://github.com/zeeshankanuga/e-commerce-app.git'
                // }
            }
        }
        stage('Build') {
            steps {
                // Build the Docker image with the Jenkins pipeline number as the tag
                script {
                    def imageName = "e-commerce-app:${env.BUILD_NUMBER}"
                    sh """
                    DOCKER_BUILDKIT=0 docker build --pull -t ${imageName} .
                    """
                }
                // Build migration image (path /scripts/Dockerfile.migration)
                script {
                    def migrationImageName = "db-migration:${env.BUILD_NUMBER}"
                    sh """
                    DOCKER_BUILDKIT=0 docker build --pull -t ${migrationImageName} -f scripts/Dockerfile.migration .
                    """
                }
            }
        }
        stage('Push') {
            steps {
                // Push the Docker image to the registry
                script {

                    def imageName = "e-commerce-app:${env.BUILD_NUMBER}"
                    sh "docker tag ${imageName} zeeshankanuga/${imageName}"

            // Make sure 'docker-hub-credentials' matches the ID you created in Jenkins UI
            withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials',
                                             usernameVariable: 'DOCKER_USER', 
                                             passwordVariable: 'DOCKER_PASSWORD')]) {
                
                // Securely pipe the password into the login command
                sh "echo '${DOCKER_PASSWORD}' | docker login -u '${DOCKER_USER}' --password-stdin"
                                             }

                    sh "docker push zeeshankanuga/${imageName}"
                }

                // Push the Docker migration image to the registry
                script {
                    def migrationImageName = "db-migration:${env.BUILD_NUMBER}"
                    sh "docker tag ${migrationImageName} zeeshankanuga/${migrationImageName}"
                    sh "docker push zeeshankanuga/${migrationImageName}"
                }
                
                // Clean up local Docker images to save space
                sh "docker system prune -a -f"
            }
        }
    }

}