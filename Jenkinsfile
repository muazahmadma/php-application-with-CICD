@Library("Jenkins_shared_libraries") _

pipeline {
    agent any

    environment {
        APP_NAME        = "php-app"       // Sirf project name
        IMAGE_TAG       = "${BUILD_NUMBER}"
        DOCKERHUB_CREDS = "dockerhubcred" // Jenkins Credential ID
    }

    stages {
        stage("Code Checkout") {
            steps {
                script {
                    code_clone("https://github.com/alisarfraz13/php-application-with-CICD.git", "main")
                }
            }
        }

        stage("Build Image") {
            steps {
                script {
                    buildDockerImage(
                        credentialsId: env.DOCKERHUB_CREDS,
                        imageName:     env.APP_NAME,
                        buildTag:      env.IMAGE_TAG
                    )
                }
            }
        }

        stage("Push Image") {
            steps {
                script {
                    pushDockerImage(
                        credentialsId: env.DOCKERHUB_CREDS,
                        imageName:     env.APP_NAME,
                        buildTag:      env.IMAGE_TAG
                    )
                }
            }
        }

        stage("Deploy & Cleanup") {
            steps {
                script {
                    cleanAndDeploy(
                        credentialsId: env.DOCKERHUB_CREDS,
                        imageName:     env.APP_NAME,
                        newBuildTag:   env.IMAGE_TAG
                    )
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo "✅ Deployment Complete. App is running on Build: ${env.IMAGE_TAG}"
        }
    }
}