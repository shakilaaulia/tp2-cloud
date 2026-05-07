pipeline {
    agent any

    environment {
        DOCKER_USER = "shakilaaulia245"
        GIT_REPO_URL = "https://github.com/shakilaaulia/tp2-cloud.git"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: "${GIT_REPO_URL}"
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-login',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    bat """
                    docker login -u %DOCKER_USER% -p %DOCKER_PASS%
                    """
                }
            }
        }

        stage('Build Backend') {
            steps {
                bat 'docker build -t shakilaaulia245/hiburan-backend:latest ./backend'
            }
        }

        stage('Build Frontend') {
            steps {
                bat 'docker build -t shakilaaulia245/hiburan-frontend:latest ./frontend'
            }
        }

        stage('Push Backend') {
            steps {
                bat 'docker push shakilaaulia245/hiburan-backend:latest'
            }
        }

        stage('Push Frontend') {
            steps {
                bat 'docker push shakilaaulia245/hiburan-frontend:latest'
            }
        }

        stage('Deploy to AKS') {
            steps {
                withKubeConfig([credentialsId: 'aks-config']) {

                    bat 'kubectl apply -f k8s.yaml'
                    bat 'kubectl apply -f ingress.yaml'

                    bat 'kubectl rollout restart deployment hiburan-backend'
                    bat 'kubectl rollout restart deployment hiburan-frontend'
                }
            }
        }
    }
}