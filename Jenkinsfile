# Jenkins file for building and deploying the application
# fetching the code from the repository and running the build and deployment stages
# fetch code
# build image with jenkins pipeline number
# apply tag to the image (eg. zeeshankanuga/onineshop:jenkins-pipeline-number)
# push image to docker registry

pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Checkout code from the repository
                git 'https://github.com/zeeshankanuga/e-commerce-app.git'}
            }
        }
        stage('Build') {
            steps {
                // Build the Docker image with the Jenkins pipeline number as the tag
                script {
                    def imageName = "e-commerce-app:${env.BUILD_NUMBER}"
                    sh "docker build -t ${imageName} ."
                }
            }
        }
        stage('Push') {
            steps {
                // Push the Docker image to the registry
                script {
                    def imageName = "e-commerce-app:${env.BUILD_NUMBER}"
                    sh "docker tag ${imageName} zeeshankanuga/${imageName}"
                    sh "docker push zeeshankanuga/${imageName}"
                }
            }
        }
    }
}
