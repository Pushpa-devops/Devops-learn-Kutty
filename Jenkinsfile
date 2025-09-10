pipeline {
    agent any

    environment {
        IMAGE_NAME = "nginx-jenkins-minikube"
        KUBECONFIG_PATH = "/var/lib/jenkins/.kube/config"
    }

    triggers {
        // Poll GitHub every 1 hour
        pollSCM('H * * * *')
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Pushpa-devops/Devops-learn-Kutty.git'
            }
        }

        stage('Start Minikube') {
            steps {
                sh '''
                #!/bin/bash
                if ! minikube status >/dev/null 2>&1; then
                    echo "Starting Minikube..."
                    minikube start --driver=docker
                fi
                minikube status
                '''
            }
        }

        stage('Build Docker Image in Minikube') {
            steps {
                sh '''
                #!/bin/bash
                echo "Building image inside Minikube..."
                minikube image build -t ${IMAGE_NAME}:latest .
                minikube image ls | grep ${IMAGE_NAME}
                '''
            }
        }

        stage('Deploy to Minikube') {
            steps {
                sh '''
                #!/bin/bash
                export KUBECONFIG=${KUBECONFIG_PATH}
                echo "Deploying app to Minikube..."
                kubectl apply -f k8s-deployment.yaml
                kubectl apply -f k8s-service.yaml
                kubectl rollout status deployment/nginx-demo
                kubectl get pods
                kubectl get svc
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful!"
            echo "Run this to access your app:"
            echo "minikube service nginx-demo-service --url"
        }
        failure {
            echo "❌ Deployment failed. Check logs."
        }
    }
}
