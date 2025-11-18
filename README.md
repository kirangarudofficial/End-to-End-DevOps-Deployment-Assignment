

# 🚀 End-to-End DevOps Deployment Assignment

## 👤 Author

**Kiran Garud**  
DevOps Fresher

## 📌 Project Overview

This project demonstrates a complete CI/CD pipeline for a simple Flask application using:

- Jenkins
- Docker
- Docker Hub
- AWS EC2
- GitHub

The pipeline automates:

- Checkout code from GitHub
- Build Docker image
- Login & push to Docker Hub
- Pull latest image on EC2
- Deploy and run the container
- Validate /hello API endpoint

## 📡 Application Details

| Component | Details |
|-----------|---------|
| Language | Python Flask |
| Endpoint | GET /hello |
| Response | "Hello from DevOps – Kiran Garud 🚀" |
| App Port | 5000 |
| Docker Image Name | devops-flask-app |
| Docker Hub Repo | kgarud30/devops-flask-app |

## 📁 Project Structure

```
EndToEndProject/
│
├── app.py               # Flask Application
├── requirements.txt     # Python Dependencies
├── Dockerfile           # Docker configuration
├── Jenkinsfile          # Jenkins Pipeline Script
└── README.md            # Project Documentation
```

## 🧩 Step 1: Flask Application (app.py)

```python
from flask import Flask
app = Flask(__name__)

@app.route('/hello')
def hello():
    return "Hello from DevOps – Kiran Garud 🚀"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

## 🐳 Step 2: Dockerfile

```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python3", "app.py"]
```

## 🧪 Step 3: Test Application Locally

```bash
pip3 install flask
python3 app.py
```

Access URL:
```
http://<EC2-Public-IP>:5000/hello
```

## 📦 Step 4: Push Code to GitHub

```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/kirangarudofficial/End-to-End-DevOps-Deployment-Assignment.git
git push -u origin main
```

## 🔧 Step 5: Jenkins Setup

Install Required Plugins:

- Git
- Pipeline
- Docker Pipeline
- Credentials Binding

Add Docker Hub credentials:

- ID: dockerhub
- Save username & password

## ⚙️ Step 6: CI/CD Pipeline (Jenkinsfile)

Pipeline performs:

- Clean workspace
- Checkout GitHub code
- Build Docker image
- Login to Docker Hub
- Tag & push image
- Deploy container on EC2
- Validate /hello endpoint

Full pipeline is inside Jenkinsfile.

## 🐳 Step 7: Docker Hub

Repository: kgarud30/devops-flask-app

Jenkins automatically pushes images here.

## 🚀 Step 8: Deployment on EC2

Run on EC2:

```bash
# Stop existing container
docker stop devops-flask-app || true
docker rm devops-flask-app || true

# Pull latest image
docker pull kgarud30/devops-flask-app:latest

# Run new container
docker run -d -p 5000:5000 --name devops-flask-app kgarud30/devops-flask-app
```

Check logs:

```bash
docker ps
docker logs devops-flask-app
```

Access API:
```
http://<EC2-Public-IP>:5000/hello
```

## 📸 Screenshots (Add in GitHub)

- ✔️ Jenkins Pipeline Success
- ✔️ Browser response of /hello endpoint

## 🛠️ Tools & Technologies

- AWS EC2
- Jenkins
- Docker
- Docker Hub
- Git / GitHub
- Python Flask

## ⭐ Author Note

This project demonstrates a fully automated beginner-friendly DevOps CI/CD workflow, covering:

- Dockerization
- CI/CD with Jenkins
- Image registry management
- EC2 deployment
- End-to-end pipeline execution
