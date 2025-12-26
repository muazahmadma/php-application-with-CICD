@Library("Jenkins_shared_libraries@main") _

pipeline {
    agent { label "dev" }

    environment {
        DISCORD_WEBHOOK = credentials('discord-webhook-url')
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {

        stage("Code Clone") {
            steps {
                script {
                    discord_notify('STARTED')
                    code_clone("https://github.com/muazahmadma/php-application-with-CICD.git","main")
                }
            }
        }

        stage('Trivy FileSystem Scan') {
            steps {
                sh "trivy fs --severity HIGH,CRITICAL --exit-code 1 ."
            }
        }       

        stage('Sonarqube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=php-app -Dsonar.projectName=php-app"
                }
            }
        }

        stage("Quality Gate") {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage("Build") {
            steps {
                script{
                    env.VERSION_TAG = "${env.BUILD_NUMBER}"
                    sh "docker build . -t php-app"
                }
            }
        }

        stage("Trivy Image Scan") {
            steps {
                sh "trivy image --severity HIGH,CRITICAL --exit-code 1 php-app:latest"
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