pipeline {
    agent any

    environment {
        IMAGE_NAME = "nginx-jenkins-minikube"
        KUBECONFIG_PATH = "/var/lib/jenkins/.kube/config"
    }

    triggers {
        pollSCM('H * * * *')  // Poll GitHub every hour
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Pushpa-devops/Devops-learn-Kutty.git'
            }
        }

        stage('Start Minikube if Needed') {
            steps {
                sh '''
                STATUS=$(minikube status --format '{{.Host}} {{.Kubelet}} {{.APIServer}}')
                if [[ "$STATUS" != *"Running Running Running"* ]]; then
                    echo "Starting Minikube..."
                    minikube start --driver=docker --memory=4096 --cpus=2 --addons=storage-provisioner
                fi
                '''
            }
        }

        stage('Configure Docker for Minikube') {
            steps {
                sh '''
                echo "Pointing Docker CLI to Minikube..."
                eval $(minikube docker-env)
                docker version
                '''
            }
        }

        stage('Clean Old Deployment & Pods') {
            steps {
                sh '''
                echo "Deleting old deployment and pods..."
                kubectl delete deployment nginx-demo --ignore-not-found
                kubectl delete pod -l app=nginx-demo --ignore-not-found
                '''
            }
        }

        stage('Build Docker Image Inside Minikube') {
            steps {
                sh '''
                echo "Building Docker image inside Minikube..."
                docker build -t ${IMAGE_NAME}:latest .
                docker images | grep ${IMAGE_NAME}
                '''
            }
        }

        stage('Deploy to Minikube') {
            steps {
                sh '''
                export KUBECONFIG=${KUBECONFIG_PATH}
                echo "Applying Deployment and Service..."
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
            echo "Access app using: minikube service nginx-demo-service --url"
        }
        failure {
            echo "❌ Pipeline failed. Check logs."
        }
    }
}
