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
                    credentialsId: 'docker-baru',
                    usernameVariable: 'D_USER',
                    passwordVariable: 'D_PASS'
                )]) {
                    // Menggunakan --password-stdin agar lebih aman sesuai saran log docker
                    bat "echo \"%D_PASS%\" | docker login -u %D_USER% --password-stdin"
                }
            }
        }

        stage('Build & Push Images') {
            steps {
                bat """
                docker build -t %DOCKER_USER%/hiburan-backend:latest ./backend
                docker build -t %DOCKER_USER%/hiburan-frontend:latest ./frontend
                docker push %DOCKER_USER%/hiburan-backend:latest
                docker push %DOCKER_USER%/hiburan-frontend:latest
                """
            }
        }

        stage('Deploy to AKS') {
            steps {
                withKubeConfig([credentialsId: 'aks-config']) {
                    // 1. Pastikan nama file sesuai: hiburan-k8s.yaml
                    bat 'kubectl apply -f hiburan-k8s.yaml'
                    
                    // 2. Restart deployment agar menarik image terbaru
                    // Nama harus match dengan 'metadata.name' di file YAML
                    bat 'kubectl rollout restart deployment backend-hiburan'
                    bat 'kubectl rollout restart deployment frontend-hiburan'
                }
            }
        }
    }
}