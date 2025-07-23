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

        stage('Kubernetes Debug Info') {
            steps {
                sh '''
                    echo "🔍 Kubernetes Debug Info"
                    echo "Running as: $(whoami)"
                    echo "Using kubeconfig: $KUBECONFIG"
                    ls -la $KUBECONFIG
                    echo "🔸 Config View"
                    kubectl config view
                    echo "🔸 Current Context"
                    kubectl config current-context
                    echo "🔸 Get Nodes"
                    kubectl get nodes
                    echo "🔸 Get Pods (all namespaces)"
                    kubectl get pods -A
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
