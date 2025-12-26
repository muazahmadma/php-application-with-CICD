@Library("Jenkins_shared_libraries@main") _

pipeline {
    agent { label "dev" }

    environment {
        DISCORD_WEBHOOK = credentials('discord-webhook-url')
    }

    stages {

        stage("Code") {
            steps {
                script {
                    discord_notify('STARTED')
                    code_clone("https://github.com/muazahmadma/php-application-with-CICD.git","main")
                }
            }
        }

        stage("Build & Test") {
            steps {
                script{
                    env.VERSION_TAG = "${env.BUILD_NUMBER}"
                    sh "docker build . -t php-app"
                }
            }
        }

        stage("Push to DockerHub") {
            steps {
                script {
                    docker_push("dockerhub-creds", "php-app", env.VERSION_TAG)
                }
            }
        }

        stage("Deploy") {
            steps {
                sh "docker compose down && docker compose up -d"
                sh "docker image prune -a -f"
            }
        }
    }

    post {
        success {
            discord_notify('SUCCESS')
        }
        failure {
            discord_notify('FAILURE')
        }
    }
}