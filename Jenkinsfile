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
                // You can replace this with your actual Git checkout logic if 
