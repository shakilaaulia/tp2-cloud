pipeline {
    agent any

    environment {
        DOCKER_IMAGE_BACKEND = "shakilaaulia245/hiburan-backend:latest"
        DOCKER_IMAGE_FRONTEND = "shakilaaulia245/hiburan-frontend:latest"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/shakilaaulia/tp2-cloud.git'
            }
        }

        stage('Build Backend') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE_BACKEND ./backend'
            }
        }

        stage('Build Frontend') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE_FRONTEND ./frontend'
            }
        }

        stage('Docker Login') {
            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-login',
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS'
                    )
                ]) {

                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                }
            }
        }

        stage('Push Backend') {
            steps {
                sh 'docker push $DOCKER_IMAGE_BACKEND'
            }
        }

        stage('Push Frontend') {
            steps {
                sh 'docker push $DOCKER_IMAGE_FRONTEND'
            }
        }

        stage('Deploy to AKS') {
            steps {

                sh 'kubectl apply -f hiburan-k8s.yaml'

                sh 'kubectl apply -f hiburan-ingress.yaml'

                sh 'kubectl rollout restart deployment backend-hiburan'

                sh 'kubectl rollout restart deployment frontend-hiburan'
            }
        }
    }
}