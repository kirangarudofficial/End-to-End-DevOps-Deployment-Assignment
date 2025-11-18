pipeline {
    agent any

    environment {
        DOCKERHUB_REPO = "kirangarud/devops-flask-app"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/kirangarudofficial/End-to-End-DevOps-Deployment-Assignment.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                echo "Building Docker Image..."
                docker build -t devops-flask-app .
                '''
            }
        }

        stage('Docker Hub Login') {
            steps {
                withCredentials([usernamePassword(
                credentialsId: 'dockerhub',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo "Logging into Docker Hub..."
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh '''
                echo "Tagging Docker Image..."
                docker tag devops-flask-app:latest $DOCKERHUB_REPO:latest
                '''
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                sh '''
                echo "Pushing Docker Image..."
                docker push $DOCKERHUB_REPO:latest
                '''
            }
        }

        stage('Deploy Application') {
            steps {
                sh '''
                echo "Stopping old container..."
                docker stop devops-flask-app || true

                echo "Removing old container..."
                docker rm devops-flask-app || true

                echo "Pulling latest image..."
                docker pull $DOCKERHUB_REPO:latest

                echo "Starting new container..."
                docker run -d -p 5000:5000 --name devops-flask-app $DOCKERHUB_REPO:latest
                '''
            }
        }
    }

    post {
        success {
            echo "CI/CD Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed. Check logs."
        }
    }
}
