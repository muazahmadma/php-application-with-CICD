pipeline {
    agent {
        node {
            label 'docker-node'
        }
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        IMAGE_NAME = "your_dockerhub_username/laravel-app"
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
        timeout(time: 15, unit: 'MINUTES')
        disableConcurrentBuilds()
    }

    stages {
        stage('📥 Checkout') {
            steps {
                echo "GitHub se code pull kar rahe hain..."
                git([
                    url: 'https://github.com/your-username/php-application-with-CICD.git',
                    branch: 'main'
                ])
            }
        }

        stage('🔨 Build & Push') {
            steps {
                echo "Docker image build ho rahi hai..."
                sh '''
                    # Login
                    echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                    
                    # Build image
                    docker build -t ${IMAGE_NAME}:latest .
                    
                    # Push to Docker Hub
                    docker push ${IMAGE_NAME}:latest
                    
                    # Logout (security)
                    docker logout
                '''
            }
        }

        stage('🚀 Deploy') {
            steps {
                echo "Production mein deploy ho rahe hain..."
                sh '''
                    cd /home/ec2-user/laravel-app
                    
                    # Stop old containers
                    docker-compose down
                    
                    # Pull latest image aur containers start karo
                    docker-compose up -d
                    
                    # Check status
                    docker-compose ps
                    
                    # Database migrations
                    docker-compose exec -T php php artisan migrate --force
                    
                    # Cache clear
                    docker-compose exec -T php php artisan cache:clear
                '''
            }
        }

        stage('🧹 Cleanup') {
            steps {
                sh 'docker image prune -a --force --filter "until=48h"'
            }
        }
    }

    post {
        success {
            echo "✅ Build #${BUILD_NUMBER} successfully deployed!"
        }
        failure {
            echo "❌ Build #${BUILD_NUMBER} failed! Check logs"
        }
    }
}
