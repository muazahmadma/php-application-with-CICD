pipeline {
    agent { label "dev"};
    
    stages{
        stage("Code"){
            steps{
                git url: "https://github.com/muazahmadma/php-application-with-CICD.git", branch: "main"
            }
        }
        stage("Build & Test"){
            steps{
                sh "docker build . -t php-app"
            }
        }
        stage("Push to DockerHub"){
            steps{
                withCredentials([usernamePassword(credentialsId:"dockerhub-creds",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")]){
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker tag php-app ${env.dockerHubUser}/php-app:latest"
                    sh "docker push ${env.dockerHubUser}/php-app:latest" 
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