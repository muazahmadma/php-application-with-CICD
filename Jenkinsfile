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
                    code_clone("https://github.com/muazahmadma/php-application-with-CICD.git","main")
                }
            }
        }

        stage("Build & Test") {
            steps {
                sh "docker build . -t php-app"
            }
        }

        stage("Push to DockerHub") {
            steps {
                script {
                    docker_push("dockerhub-creds", "php-app")
                }
            }
        }

        stage("Deploy") {
            steps {
                sh "docker compose down && docker compose up -d"
            }
        }
    }

    post {
        started {
            discord_notify('STARTED')
        }
        success {
            discord_notify('SUCCESS')
        }
        failure {
            discord_notify('FAILURE')
        }
    }
}