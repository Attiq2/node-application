pipeline {
    agent any

    environment {
        IMAGE_NAME = 'docker.io/attiq1124/devops-node-app'
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
                    echo "User ID Info:"
                    id
                    echo "Checking Docker containers:"
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

        stage('Kube Debug') {
            steps {
                sh '''
                    echo "🔍 Kube Config Debug Info"
                    echo "KUBECONFIG=$KUBECONFIG"
                    echo "Current user: $(whoami)"
                    id
                    echo "Environment Variables:"
                    env
                    echo "Checking permissions:"
                    ls -l /var/lib/jenkins/.kube/config || true
                    ls -l /root/.minikube/ca.crt || true
                    ls -l /root/.minikube/profiles/minikube/client.key || true
                    echo "kubectl config view:"
                    kubectl config view || true
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
