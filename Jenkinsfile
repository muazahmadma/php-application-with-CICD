@Library("Jenkins_shared_libraries") _

pipeline {
    agent { label "Node" }
    
    environment {
        IMAGE_NAME = "php-app"
        BUILD_TAG = "${BUILD_NUMBER}"
        REGISTRY_USER = credentials('dockerhub-creds').username
        DOCKERHUB_CREDS = "dockerhub-creds"
    }
    
    stages {
        stage("Code Checkout") {
            steps {
                code_clone("https://github.com/alisarfraz13/php-application-with-CICD.git", "main")
            }
        }
        
        stage("Build") {
            steps {
                buildDockerImage(imageName: "${IMAGE_NAME}", buildTag: "${BUILD_TAG}")
            }
        }
        
        stage("Push") {
            steps {
                pushDockerImage(
                    credentialsId: "${DOCKERHUB_CREDS}",
                    registryUser: "${REGISTRY_USER}",
                    imageName: "${IMAGE_NAME}",
                    buildTag: "${BUILD_TAG}"
                )
            }
        }
        
        stage("Deploy") {
            steps {
                cleanAndDeploy(
                    registryUser: "${REGISTRY_USER}",
                    imageName: "${IMAGE_NAME}",
                    newBuildTag: "${BUILD_TAG}",
                    containerName: "php-app-container"
                )
            }
        }
        
        stage("Status Check") {
            steps {
                statusCheck(imageName: "${IMAGE_NAME}")
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
