pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'mamatha0124/python-app:latest' // Docker image name with tag
        K8S_NAMESPACE = 'default' // Kubernetes namespace
        DEPLOYMENT_YAML = 'deployment.yaml'
        SERVICE_YAML = 'service.yaml'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Repository already cloned by Jenkins.'
                // If needed: git 'https://github.com/Mamatha1206/java.git'
            }
        }

        stage('Package App') {
            steps {
                echo 'Packaging the application...'
                sh 'zip -r app.zip . -x "*.git*"'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                // ✅ FIXED: Removed extra :latest
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Login & Push to DockerHub') {
            steps {
                echo 'Logging in to Docker Hub and pushing the image...'
                withDockerRegistry([credentialsId: 'DOCKER-HUB-CREDENTIALS', url: '']) {
                    // ✅ FIXED: Removed extra :latest
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo 'Deploying application to Kubernetes...'
                sh "kubectl apply -f $DEPLOYMENT_YAML"
                sh "kubectl apply -f $SERVICE_YAML"
            }
        }

        stage('Scale the Application') {
            steps {
                echo 'Scaling application to 3 replicas...'
                sh 'kubectl scale deployment python-app-deployment --replicas=3'
            }
        }

        stage('Verify Deployment') {
            steps {
                echo 'Verifying Kubernetes deployment...'
                sh 'kubectl get pods'
                sh 'kubectl get svc python-app-service'
            }
        }

        stage('Scale Back Down') {
            steps {
                echo 'Scaling application back down to 1 replica...'
                sh 'kubectl scale deployment python-app-deployment --replicas=1'
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline successfully completed!"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}
