pipeline {
    agent any

    environment {
        KUBECONFIG = '/var/lib/jenkins/.kube/config'
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
                    echo "User: $(whoami)"
                    echo "Groups: $(groups)"
                    docker ps
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t Attiq2/devops-node-app .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh 'docker push Attiq2/devops-node-app'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yml'
            }
        }
    }
}

