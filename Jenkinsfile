pipeline {
    agent any

    environment {
        IMAGE_BACKEND = 'shakilaaulia245/hiburan-backend:latest'
        IMAGE_FRONTEND = 'shakilaaulia245/hiburan-frontend:latest'
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
                bat 'docker build -t %IMAGE_BACKEND% ./backend'
            }
        }

        stage('Build Frontend') {
            steps {
                bat 'docker build -t %IMAGE_FRONTEND% ./frontend'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-login',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {

                    bat 'echo %PASS% | docker login -u %USER% --password-stdin'
                }
            }
        }

        stage('Push Backend') {
            steps {
                bat 'docker push %IMAGE_BACKEND%'
            }
        }

        stage('Push Frontend') {
            steps {
                bat 'docker push %IMAGE_FRONTEND%'
            }
        }

        stage('Deploy to AKS') {
            steps {
                withCredentials([file(credentialsId: 'aks-config', variable: 'KUBECONFIG')]) {

                    bat 'kubectl apply -f hiburan-k8s.yaml'
                    bat 'kubectl apply -f hiburan-ingress.yaml'
                }
            }
        }
    }
}