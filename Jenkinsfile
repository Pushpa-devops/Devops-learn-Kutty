stage('Deploy Nginx') {
    steps {
        sh '''
        #!/bin/bash
        export KUBECONFIG=${KUBECONFIG_PATH}

        echo "Creating simple Nginx deployment..."
        kubectl apply -f - <<EOF
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
        kubectl apply -f - <<EOF
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
