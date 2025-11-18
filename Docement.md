

Of course! Here is the project documentation transformed into a professional and well-structured `README.md` file, perfect for a GitHub repository.

---

# 🚀 End-to-End DevOps Deployment: Flask CI/CD Pipeline

[![Jenkins](https://img.shields.io/badge/Jenkins-CCI/CD-blue?logo=jenkins)](https://www.jenkins.io/)
[![Docker](https://img.shields.io/badge/Docker-Container-blue?logo=docker)](https://www.docker.com/)
[![Flask](https://img.shields.io/badge/Flask-Web%20App-green?logo=flask)](https://flask.palletsprojects.com/)
[![AWS](https://img.shields.io/badge/AWS-EC2-orange?logo=amazon-aws)](https://aws.amazon.com/ec2/)
[![Git](https://img.shields.io/badge/Git-Version%20Control-red?logo=git)](https://git-scm.com/)

This project demonstrates a complete, automated CI/CD (Continuous Integration/Continuous Deployment) pipeline for a simple Python Flask web application. It showcases the practical application of core DevOps principles, including Infrastructure as Code, containerization with Docker, version control with Git, and pipeline automation with Jenkins.

The final outcome is a robust, repeatable process for deploying application updates with a single `git push` to a repository.

## 📋 Table of Contents

1.  [🏗️ Architecture Overview](#-architecture-overview)
2.  [✨ Key Features](#-key-features)
3.  [🛠️ Prerequisites](#️-prerequisites)
4.  [📁 Project Structure](#-project-structure)
5.  [🚀 Setup and Installation](#-setup-and-installation)
    *   [1. AWS EC2 Instance Setup](#1-aws-ec2-instance-setup)
    *   [2. Software Installation on EC2](#2-software-installation-on-ec2)
    *   [3. Jenkins Initial Setup](#3-jenkins-initial-setup)
6.  [🐳 Application and Containerization](#-application-and-containerization)
7.  [⚙️ CI/CD Pipeline with Jenkins](#️-cicd-pipeline-with-jenkins)
    *   [Jenkins Configuration](#jenkins-configuration)
    *   [The Jenkinsfile](#the-jenkinsfile)
8.  [🔧 Deployment and Verification](#-deployment-and-verification)
9.  [🎯 Conclusion & Key Takeaways](#-conclusion--key-takeaways)
10. [🚀 Future Enhancements](#-future-enhancements)
11. [👤 Author](#-author)

---

## 🏗️ Architecture Overview

The pipeline automates the flow from code commit to production deployment on an AWS EC2 instance.

<img width="1024" height="1024" alt="Arcchiteure" src="https://github.com/user-attachments/assets/fed31c71-05ff-413e-968a-199ed8a46461" />


## ✨ Key Features

-   **Fully Automated CI/CD**: From code commit to production deployment with zero manual intervention.
-   **Containerization**: The application is packaged into a Docker image, ensuring consistency across environments.
-   **Infrastructure as Code**: The entire pipeline is defined in a `Jenkinsfile`, version-controlled with the application code.
-   **Cloud-Native Deployment**: Utilizes AWS EC2 for scalable and reliable hosting.
-   **Validation**: Includes a stage to validate that the deployed application is responding correctly.

## 🛠️ Prerequisites

Before you begin, ensure you have the following:

-   An **AWS Account**.
-   A **GitHub Account**.
-   A **Docker Hub Account**.
-   An SSH client (like Terminal or PuTTY).

---

## 📁 Project Structure

The project is organized into a single directory with the following key files:

```
EndToEndProject/
│
├── app.py               # The main Flask application logic
├── requirements.txt     # Python dependencies for the app
├── Dockerfile           # Instructions to containerize the app
├── Jenkinsfile          # The Jenkins pipeline definition
└── README.md            # This file
```

---

## 🚀 Setup and Installation

### 1. AWS EC2 Instance Setup

1.  **Launch Instance**: Go to the AWS Management Console and launch a new EC2 instance.
    *   **Instance Type**: `t2.medium` (recommended to run Jenkins and Docker smoothly).
    *   **AMI**: Amazon Linux 2 AMI.
    *   **Security Group**: Configure inbound rules to allow traffic on:
        *   **SSH (Port 22)**
        *   **HTTP (Port 8080)** for Jenkins
        *   **Custom TCP (Port 5000)** for the Flask App
    *   **Key Pair**: Create and download a `.pem` key file for secure SSH access.

### 2. Software Installation on EC2

Connect to your EC2 instance via SSH and run the following commands:

```bash
# Update the system
sudo yum update -y

# Install Git, Java, Python
sudo yum install -y git java-1.8.0-openjdk-devel python3 python3-pip

# Install Docker
sudo yum install -y docker
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -a -G docker ec2-user  # Add user to docker group
# IMPORTANT: Log out and log back in via SSH for this to take effect.

# Install Jenkins
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
sudo yum install -y jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

### 3. Jenkins Initial Setup

1.  Access Jenkins at `http://<EC2-Public-IP>:8080`.
2.  Retrieve the initial admin password: `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.
3.  Complete the setup by installing the suggested plugins.
4.  Create an admin user.

---

## 🐳 Application and Containerization

The core of the project is a simple Flask application, containerized for portability.

### `app.py`
A minimal Flask web server that responds to a single API endpoint.

```python
from flask import Flask
app = Flask(__name__)

@app.route('/hello')
def hello():
    return "Hello from DevOps – Kiran Garud 🚀"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

### `requirements.txt`
Specifies the Python dependency for the application.

```
Flask==2.0.1
```

### `Dockerfile`
Instructions to package the Flask app into a Docker image.

```dockerfile
# Use an official Python runtime as a parent image
FROM python:3.9-slim

# Set the working directory in the container
WORKDIR /app

# Copy the dependencies file and install them
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of the application code
COPY . .

# Expose the port the app runs on
EXPOSE 5000

# Define the command to run the application
CMD ["python3", "app.py"]
```

---

## ⚙️ CI/CD Pipeline with Jenkins

The automation is handled by a Jenkins pipeline defined in a `Jenkinsfile`.

### Jenkins Configuration

1.  **Install Plugins**: Ensure `Git`, `Pipeline`, `Docker Pipeline`, and `Credentials Binding` plugins are installed in Jenkins.
2.  **Add Docker Hub Credentials**:
    *   Navigate to `Manage Jenkins` > `Manage Credentials`.
    *   Add a new `Username with password` credential.
    *   Enter your Docker Hub username and password.
    *   Set the **ID** to `dockerhub`. This ID is used in the `Jenkinsfile` to authenticate.

### The Jenkinsfile

This file defines the entire automated workflow. It is stored in the root of the Git repository.

```groovy
pipeline {
    agent any

    environment {
        DOCKERHUB_REPO = "kgarud30/devops-flask-app" // <-- CHANGE THIS TO YOUR DOCKERHUB REPO
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
                    url: 'https://github.com/kirangarudofficial/End-to-End-DevOps-Deployment-Assignment.git' // <-- CHANGE THIS TO YOUR REPO URL
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
```

---

## 🔧 Deployment and Verification

1.  **Push to GitHub**: Commit all the project files (`app.py`, `requirements.txt`, `Dockerfile`, `Jenkinsfile`) to your GitHub repository.
2.  **Create Jenkins Pipeline**:
    *   In the Jenkins UI, click `New Item`.
    *   Enter a name (e.g., `Flask-App-Pipeline`) and select `Pipeline`.
    *   Under "Pipeline", select `Pipeline script from SCM`.
    *   Choose `Git` and enter your repository URL.
    *   Ensure the script path is `Jenkinsfile`.
    *   Click `Save`.
3.  **Run the Pipeline**: Click `Build Now` in your Jenkins project.
4.  **Access the Application**: Once the pipeline completes successfully, open your browser and navigate to:

    `http://<EC2-Public-IP>:5000/hello`

You should see the message: **"Hello from DevOps – Kiran Garud 🚀"**.

---

## 🎯 Conclusion & Key Takeaways

This project was a comprehensive exercise in building a modern DevOps pipeline. It successfully demonstrates:

*   **Infrastructure Provisioning**: Setting up a cloud server from scratch.
*   **Containerization**: Packaging a Python app into a portable Docker image.
*   **CI/CD Implementation**: Creating a fully automated pipeline that builds and deploys the application on every code change.
*   **Artifact Management**: Using Docker Hub as a central repository for the containerized application.

The most significant takeaway was the power of automation. By defining the entire process in code (`Jenkinsfile`, `Dockerfile`), manual, error-prone deployment steps were eliminated, creating a reliable and scalable workflow.

---

## 🚀 Future Enhancements

This project provides a solid foundation that can be extended with more advanced DevOps practices:

*   **Automated Testing**: Add a dedicated stage to run unit and integration tests before building the Docker image.
*   **Kubernetes Deployment**: Deploy to a Kubernetes cluster (e.g., Amazon EKS) for better scalability and resilience.
*   **Blue-Green Deployment**: Implement a blue-green deployment strategy for zero-downtime deployments.
*   **Infrastructure as Code (IaC)**: Use Terraform to define and provision the EC2 instance and security groups, making the infrastructure setup repeatable and version-controlled.

---

## 👤 Author

**Kiran Garud**  
DevOps Fresher

This project was created to demonstrate a practical understanding of the DevOps lifecycle and to build a foundational CI/CD skill set.
