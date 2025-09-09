pipeline {
    agent any

    environment {
        IMAGE_NAME = "nginx-jenkins-minikube"
        KUBECONFIG_PATH = "/var/lib/jenkins/.kube/config"  // Jenkins user kubeconfig
    }

    triggers {
        pollSCM('H * * * *')  // Check GitHub once every hour
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'k8s-Project', url: 'https://github.com/Pushpa-devops/Jenkins-Project.git'
            }
        }

        stage('Start Minikube if Needed') {
            steps {
                sh '''
                #!/bin/bash
                STATUS=$(minikube status --format '{{.Host}} {{.Kubelet}} {{.APIServer}}')
                if [[ "$STATUS" != *"Running Running Running"* ]]; then
                    echo "Starting Minikube with sufficient resources..."
                    minikube start --driver=docker --memory=4096 --cpus=2 --addons=storage-provisioner
                else
                    echo "Minikube already running"
                fi
                '''
            }
        }

        stage('Configure Docker to Use Minikube') {
            steps {
                sh '''
                #!/bin/bash
                echo "Pointing Docker CLI to Minikube's Docker daemon..."
                eval $(minikube docker-env)
                docker version
                '''
            }
        }

        stage('Build Docker Image Inside Minikube') {
            steps {
                sh '''
                #!/bin/bash
                echo "Building Docker image inside Minikube..."
                docker build -t ${IMAGE_NAME}:latest .
                docker images | grep ${IMAGE_NAME}
                '''
            }
        }

        stage('Deploy to Minikube') {
            steps {
                sh '''
                #!/bin/bash
                export KUBECONFIG=${KUBECONFIG_PATH}
                echo "Applying Kubernetes Deployment and Service..."

                # Use local image only
                sed -i 's|image: nginx-jenkins-minikube:latest|image: nginx-jenkins-minikube:latest\\n        imagePullPolicy: Never|' k8s-deployment.yaml

                kubectl apply -f k8s-deployment.yaml
                kubectl apply -f k8s-service.yaml

                echo "Waiting for deployment to rollout..."
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
            echo "Check URL with: minikube service nginx-demo-service --url"
        }
        failure {
            echo "❌ Pipeline failed. Check console logs."
        }
    }
}
