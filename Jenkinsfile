pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'mamatha0124/python-app' // Docker image name
         K8S_NAMESPACE = 'default' // Adjust if using a different Kubernetes namespace
        DEPLOYMENT_YAML = 'deployment.yaml' // Path to your deployment.yaml file
        SERVICE_YAML = 'service.yaml' // Path to your service.yaml file
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Repository already cloned by Jenkins.'
                // You can replace this with your actual Git checkout logic if necessary
                // git 'https://github.com/Mamatha1206/java.git'
            }
        }

        stage('Package App') {
            steps {
                echo 'Packaging the application...'
                // Create a zip package of the app, excluding the .git directory
                sh 'zip -r app.zip . -x "*.git*"'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                // Build the Docker image
                sh 'docker build -t $DOCKER_IMAGE:latest .'
            }
        }

        stage('Login & Push to DockerHub') {
            steps {
                echo 'Logging in to Docker Hub and pushing the image...'
                // Log in to Docker Hub and push the image
                withDockerRegistry([credentialsId: 'DOCKER-HUB-CREDENTIALS', url: '']) {
                    sh 'docker push $DOCKER_IMAGE:latest'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo 'Deploying application to Kubernetes...'
                // Apply the Kubernetes deployment and service YAMLs
                sh "kubectl apply -f $DEPLOYMENT_YAML"
                sh "kubectl apply -f $SERVICE_YAML"
            }
        }

        stage('Scale the Application') {
            steps {
                echo 'Scaling application to 3 replicas...'
                // Scale the Kubernetes deployment to 3 replicas
                sh 'kubectl scale deployment python-app-deployment --replicas=3'
            }
        }

        stage('Verify Deployment') {
            steps {
                echo 'Verifying Kubernetes deployment...'
                // Verify the deployment by checking the pods and service
                sh 'kubectl get pods'
                sh 'kubectl get svc python-app-service'
            }
        }

        stage('Scale Back Down') {
            steps {
                echo 'Scaling application back down to 1 replica...'
                // Scale the deployment back to 1 replica
                sh 'kubectl scale deployment python-app-deployment --replicas=1'
            }
        }
    }

    post {
        success {
            echo "Pipeline successfully completed!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
