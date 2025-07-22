pipeline {
    agent any

    environment {
        IMAGE_NAME = 'docker.io/Attiq2/devops-node-app'
        KUBECONFIG = '/var/lib/jenkins/.kube/config'
    }

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/Attiq2/node-application.git', branch: 'master'
            }
        }

        stage('Docker Debug') {
            steps {
                sh '''
                    echo "🔍 Docker Debug Info"
                    echo "User: $(whoami)"
                    echo "Groups: $(groups)"
                    docker ps
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh 'docker push $IMAGE_NAME'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yml'
            }
        }
    }
}

