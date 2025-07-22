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

        // 🔍 ADD THIS DEBUG STAGE HERE
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

        // (Your other stages here...)
    }
}


