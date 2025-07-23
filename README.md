# 🚀 Node.js CI/CD Pipeline with Jenkins, Docker, and Kubernetes

This project demonstrates a complete CI/CD pipeline setup for a sample Node.js application using Jenkins, Docker, and Kubernetes (Minikube). The pipeline automates building, testing, and deploying a containerized Node.js app.

---

## 🔧 Tech Stack

- **Node.js** – Application code
- **Jenkins** – CI/CD automation tool
- **Docker** – Containerization platform
- **Docker Hub** – Container image registry
- **Kubernetes (Minikube)** – Container orchestration platform
- **GitHub** – Source code management

---

## 📁 Project Structure

my-node-app/
├── app.js # Node.js application
├── Dockerfile # Docker build instructions
├── Jenkinsfile # Jenkins pipeline definition
├── deployment.yml # Kubernetes manifests
├── package.json # Node.js config & dependencies
└── README.md # Project documentation

## ⚙️ CI/CD Pipeline Flow

1. Code pushed to GitHub.
2. Jenkins pulls the latest code.
3. Builds Docker image from the `Dockerfile`.
4. Pushes image to Docker Hub.
5. Deploys the updated image to Kubernetes using `kubectl apply`.

---

## 🐳 Dockerfile

```Dockerfile
FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "app.js"]


##   Kubernetes Configuration
---deployment.yml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-app-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: node-app
  template:
    metadata:
      labels:
        app: node-app
    spec:
      containers:
      - name: node-app
        image: attiq1124/devops-node-app
        ports:
        - containerPort: 3000
---
apiVersion: v1
kind: Service
metadata:
  name: node-app-service
spec:
  type: NodePort
  selector:
    app: node-app
  ports:
    - port: 80
      targetPort: 3000
      nodePort: 30080
-----

## 🔐 Jenkinsfile

pipeline {
    agent any

    environment {
        IMAGE_NAME = "docker.io/attiq1124/devops-node-app"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Attiq2/node-application.git'
            }
        }

        stage('Docker Debug') {
            steps {
                sh '''
                echo "Docker Info"
                whoami
                docker ps
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'DOCKER_BUILDKIT=1 docker build -t $IMAGE_NAME .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([string(credentialsId: 'docker-pass', variable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u attiq1124 --password-stdin'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh 'docker push $IMAGE_NAME'
            }
        }

        stage('Kube Debug') {
            steps {
                sh '''
                echo "Kube Config Info"
                kubectl config view
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yml'
            }
        }
    }
}

-----

## 📦 Docker Hub Image
Name: attiq1124/devops-node-app

Link: https://hub.docker.com/r/attiq1124/devops-node-app

-----
