@Library("Jenkins_shared_libraries@main") _

pipeline {
    agent { label "dev"};
    
    stages{
        stage("Code"){
            steps{
                script{
                    code_clone("https://github.com/muazahmadma/php-application-with-CICD.git", "main")
                }
            }
        }
        stage("Build & Test"){
            steps{
                sh "docker build . -t php-app"
            }
        }
        stage("Push to DockerHub"){
            steps{
               script{
                docker_push("dockerhub-creds", "php-app")
               }
            }
        }
        stage("Deploy"){
            steps{
                sh "docker compose down && docker compose up -d"
            }
        }
    }
}