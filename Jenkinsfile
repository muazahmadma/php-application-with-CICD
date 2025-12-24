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
                cleanAndDeploy(
                    imageName: "${IMAGE_NAME}",
                    newBuildTag: "${BUILD_TAG}",
                    containerName: "php-app-container"
                )
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
            echo "✅ Pipeline has been  Executed Successfully!"
        }
        failure {
            echo "❌ Pipeline Failed - Check logs"
        }
    }
}
