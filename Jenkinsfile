pipeline {
    agent any

    environment {
    IMAGE_NAME = "nginx-jenkins-demo"
    KUBECONFIG = "/var/lib/jenkins/.kube/config"
}


    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Pushpa-devops/Devops-learn-Kutty.git'
            }
        }

       stage('Build Docker Image') {
    steps {
        sh '''
        echo "➡️ Building Docker image inside Minikube..."
        minikube image build -t nginx-jenkins-demo:latest .
        '''
    }
}

        
stage('Deploy to Minikube') {
    steps {
        sh '''
        echo "➡️ Applying Deployment..."
        kubectl apply -f k8s-deployment.yaml --kubeconfig=$KUBECONFIG
        kubectl apply -f k8s-service.yaml --kubeconfig=$KUBECONFIG
        kubectl get pods --kubeconfig=$KUBECONFIG
        kubectl get svc --kubeconfig=$KUBECONFIG
        '''
    }
}


        stage('Verify & Print URL') {
            steps {
                sh '''
                export KUBECONFIG=${KUBECONFIG_PATH}

                echo "➡️ Pods:"
                kubectl get pods -o wide

                echo "➡️ Services:"
                kubectl get svc nginx-test-service

                echo "➡️ Access Nginx using:"
                minikube service nginx-test-service --url
                '''
            }
        }
    }

    post {
        failure {
            echo "❌ Pipeline failed. Check console output."
        }
        success {
            echo "✅ Deployment successful! 🎉"
        }
    }
}
