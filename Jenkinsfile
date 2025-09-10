pipeline {
    agent any

    environment {
        KUBECONFIG_PATH = "/var/lib/jenkins/.kube/config"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Pushpa-devops/Devops-learn-Kutty.git'
            }
        }

        stage('Start Minikube') {
            steps {
                sh '''
                #!/bin/bash
                echo "Stopping any old cluster..."
                minikube stop || true
                minikube delete --all --purge || true

                echo "Starting Minikube with Docker driver..."
                minikube start --driver=docker --memory=4096 --cpus=2

                echo "✅ Minikube started with Docker driver"
                minikube status
                '''
            }
        }

        stage('Deploy Nginx') {
            steps {
                sh '''
                #!/bin/bash
                export KUBECONFIG=${KUBECONFIG_PATH}

                echo "Creating simple Nginx deployment..."
                cat <<EOF | kubectl apply -f -
                apiVersion: apps/v1
                kind: Deployment
                metadata:
                  name: nginx-test
                spec:
                  replicas: 1
                  selector:
                    matchLabels:
                      app: nginx-test
                  template:
                    metadata:
                      labels:
                        app: nginx-test
                    spec:
                      containers:
                      - name: nginx
                        image: nginx:alpine
                        ports:
                        - containerPort: 80
                EOF

                echo "Creating Service..."
                cat <<EOF | kubectl apply -f -
                apiVersion: v1
                kind: Service
                metadata:
                  name: nginx-test-service
                spec:
                  type: NodePort
                  selector:
                    app: nginx-test
                  ports:
                    - port: 80
                      targetPort: 80
                      nodePort: 30080
                EOF

                echo "Waiting for pod to be ready..."
                kubectl rollout status deployment/nginx-test
                kubectl get pods -o wide
                kubectl get svc nginx-test-service
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Nginx deployed successfully!"
            echo "Run: minikube service nginx-test-service --url"
        }
        failure {
            echo "❌ Pipeline failed. Check console output."
        }
    }
}
