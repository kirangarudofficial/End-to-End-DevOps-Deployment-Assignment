pipeline {
    agent any

    environment {
        DOCKERHUB_REPO = "kgarud30/devops-flask-app"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/kirangarudofficial/End-to-End-DevOps-Deployment-Assignment.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                echo "===== Checking Docker Installation ====="
                docker --version

                echo "===== Building Docker Image ====="
                sudo docker build -t devops-flask-app .
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
                    echo "===== Docker Hub Login ====="
                    echo "$DOCKER_PASS" | sudo docker login -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh '''
                echo "===== Tagging Docker Image ====="
                sudo docker tag devops-flask-app:latest $DOCKERHUB_REPO:latest
                '''
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                sh '''
                echo "===== Pushing Docker Image to Docker Hub ====="
                sudo docker push $DOCKERHUB_REPO:latest
                '''
            }
        }

        stage('Deploy Application') {
            steps {
                sh '''
                echo "===== Stopping Old Container ====="
                sudo docker stop devops-flask-app || true

                echo "===== Removing Old Container ====="
                sudo docker rm devops-flask-app || true

                echo "===== Pulling Latest Docker Image ====="
                sudo docker pull $DOCKERHUB_REPO:latest

                echo "===== Starting New Container ====="
                sudo docker run -d -p 5000:5000 --name devops-flask-app $DOCKERHUB_REPO:latest

                echo "===== Deployment Successful ====="
                '''
            }
        }
    }

    post {
        success {
            echo "🎉 CI/CD Pipeline executed successfully!"
        }
        failure {
            echo "❌ Pipeline failed — Check logs."
        }
    }
}
