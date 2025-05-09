pipeline {
    agent any
    environment {
        BACKEND_IMAGE = 'my-flask-app'
        FRONTEND_IMAGE = 'my-frontend'
        REGISTRY = 'docker.io/myusername'
        DOCKER_CREDENTIALS = 'docker-credentials'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/aungkowinitlay/Lab1.Jenkins.git'
            }
        }
        stage('Build Backend') {
            steps {
                dir('backend') {
                    sh 'python3 -m venv venv'
                    sh '. venv/bin/activate && pip install -r requirements.txt'
                }
            }
        }
        stage('Test Backend') {
            steps {
                dir('backend') {
                    sh '. venv/bin/activate && pytest'
                }
            }
        }
        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'html-validate index.html'
                }
            }
        }
        stage('Build Docker Images') {
            steps {
                dir('backend') {
                    sh "docker build -t ${REGISTRY}/${BACKEND_IMAGE}:${env.BUILD_NUMBER} ."
                }
                dir('frontend') {
                    sh "docker build -t ${REGISTRY}/${FRONTEND_IMAGE}:${env.BUILD_NUMBER} ."
                }
            }
        }
        stage('Push Docker Images') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh "docker push ${REGISTRY}/${BACKEND_IMAGE}:${env.BUILD_NUMBER}"
                    sh "docker push ${REGISTRY}/${FRONTEND_IMAGE}:${env.BUILD_NUMBER}"
                }
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker-compose -f docker-compose.yml up -d'
            }
        }
    }
    post {
        always {
            sh 'docker logout'
            cleanWs()
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}