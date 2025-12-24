@Library("Jenkins_shared_libraries") _

pipeline {
    agent any
    
    environment {
        IMAGE_NAME = "php-app"
        BUILD_TAG = "${BUILD_NUMBER}"
        DOCKERHUB_CREDS = "dockerhubcred"
    }
    
    stages {
        stage("Code Checkout") {
            steps {
                script{
                    code_clone("https://github.com/alisarfraz13/php-application-with-CICD.git", "main")
                }
            }
        }
        
        stage("Build") {
            steps {
                script{
                    buildDockerImage(imageName: "${IMAGE_NAME}", buildTag: "${BUILD_TAG}")
                }
            }
        }
        
        stage("Push") {
            steps {
                script{
                    pushDockerImage(
                        credentialsId: "${DOCKERHUB_CREDS}",
                        imageName: "${IMAGE_NAME}",
                        buildTag: "${BUILD_TAG}"
                    )
                }
            }
        }
        
        stage("Deploy") {
            steps {
                script{
                    withCredentials([usernamePassword(
                        credentialsId: "${DOCKERHUB_CREDS}",
                        usernameVariable: 'DOCKER_USER'
                    )]) {
                        cleanAndDeploy(
                            registryUser: "${DOCKER_USER}",
                            imageName: "${IMAGE_NAME}",
                            newBuildTag: "${BUILD_TAG}",
                            containerName: "php-app-container"
                        )
                    }
                }
            }
        }
        
        stage("Status Check") {
            steps {
                script{
                    statusCheck(imageName: "${IMAGE_NAME}")
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
        success {
            echo "✅ Pipeline Executed Successfully!"
        }
        failure {
            echo "❌ Pipeline Failed - Check logs"
        }
    }
}
