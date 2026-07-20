
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
                    docker build --pull -t ${imageName} .
                    """
                }
                // Build migration image (path /scripts/Dockerfile.migration)
                script {
                    def migrationImageName = "db-migration:${env.BUILD_NUMBER}"
                    sh """
                    docker build --pull -t ${migrationImageName} -f scripts/Dockerfile.migration .
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
            stage('Update Kubernetes Manifest') {
                steps {
                    withCredentials([usernamePassword(
                        credentialsId: 'github-credentials',
                        usernameVariable: 'GIT_USER',
                        passwordVariable: 'GIT_TOKEN'
                    )]) {
                        sh """
                        pwd
                        ls -la
                        find . -name "*.yaml"
                        """
                        sh """
                            sed -i 's|image: zeeshankanuga/e-commerce-app:.*|image: zeeshankanuga/e-commerce-app:${BUILD_NUMBER}|' kubernetes/08-deployment-app.yaml

                            sed -i 's|image: zeeshankanuga/db-migration:.*|image: zeeshankanuga/db-migration:${BUILD_NUMBER}|' kubernetes/10-job.yaml

                            git config user.email "zeeshan.kanuga@gmail.com"
                            git config user.name "Jenkins"

                            git add kubernetes/08-deployment-app.yaml kubernetes/10-job.yaml
                            git commit -m "Update images to build ${BUILD_NUMBER}" || true

                            git push https://${GIT_USER}:${GIT_TOKEN}@github.com/zeeshankanuga/e-commerce-app.git master
                        """
                    }
                }
            }

    }

}