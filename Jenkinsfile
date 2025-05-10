pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('DOCKERHUB_CREDENTIALS') // Ensure this ID matches Jenkins credentials
        BACKEND_IMAGE = "aungkowin/flask-backend:latest"
        FRONTEND_IMAGE = "aungkowin/nginx-frontend:latest"
    }
    stages {
        stage('Checkout') {
            steps {
                // Checkout code from Git repository
                git branch: 'main', url: 'https://github.com/aungkowinitlay/Lab1.Jenkins.git'
            }
        }
        stage('Build Backend Image') {
            steps {
                // Build Backend Docker image
                sh """
                    docker build -t ${BACKEND_IMAGE} -f Dockerfile.backend .
                """
            }
        }
        stage('Build Frontend Image') {
            steps {
                // Build Frontend Docker image
                sh """
                    docker build -t ${FRONTEND_IMAGE} -f Dockerfile.frontend .
                """
            }
        }
        stage('Test Backend') {
            steps {
                // Placeholder for backend tests (add actual tests if available)
                sh """
                    echo "Running backend tests..."
                    # Example: docker run ${BACKEND_IMAGE} pytest
                """
            }
        }
        stage('Login to Docker Hub') {
            steps {
                // Login to Docker Hub
                sh """
                    echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                """
            }
        }
        stage('Push Images') {
            steps {
                // Push Docker images to Docker Hub
                sh """
                    docker push ${BACKEND_IMAGE}
                    docker push ${FRONTEND_IMAGE}
                """
            }
        }
        stage('Deploy') {
            steps {
                // Run containers locally (modify for your deployment environment)
                sh """
                    docker rm -f flask-backend || true
                    docker rm -f nginx-frontend || true
                    docker run -d --name flask-backend -p 5000:5000 ${BACKEND_IMAGE}
                    docker run -d --name nginx-frontend -p 80:80 ${FRONTEND_IMAGE}
                """
            }
        }
    }
    post {
        always {
            node(label: '') {
                // Clean up Docker images and logout
                sh """
                    docker logout
                """
            }
        }
        success {
            node(label: '') {
                echo "Pipeline completed successfully!"
            }
        }
        failure {
            node(label: '') {
                echo "Pipeline failed!"
            }
        }
    }
}