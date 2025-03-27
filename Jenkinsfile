pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'komall6/javaapp:latest'
        KUBE_CONTEXT = 'minikube'
        HELM_RELEASE = 'java-app'
        HELM_CHART = './helm'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Dhanvikah/my-jenkins-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withDockerRegistry([credentialsId: 'docker-creds', url: '']) {
                        sh 'docker push $DOCKER_IMAGE'
                    }
                }
            }
        }

        stage('Deploy to Kubernetes using Helm') {
            steps {
                script {
                    sh '''
                        kubectl config use-context $KUBE_CONTEXT
                        helm upgrade --install $HELM_RELEASE $HELM_CHART --set image.repository=$DOCKER_IMAGE
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}

---

# Dockerfile
FROM openjdk:11-jre-slim
WORKDIR /app
COPY target/your-app.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]

---

# Helm Chart Structure (helm/)
helm/
├── Chart.yaml
├── values.yaml
├── templates/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml (if needed)

---

# Kubernetes Deployment YAML (helm/templates/deployment.yaml)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: your-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: your-app
  template:
    metadata:
      labels:
        app: your-app
    spec:
      containers:
        - name: your-app
          image: your-dockerhub-username/your-app:latest
          ports:
            - containerPort: 8080

---

# Kubernetes Service YAML (helm/templates/service.yaml)
apiVersion: v1
kind: Service
metadata:
  name: your-app
spec:
  type: ClusterIP
  selector:
    app: your-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
