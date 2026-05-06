pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/shakilaaulia/tp2-cloud.git'
            }
        }

        stage('Build & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-login',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {

                    // 🔥 LOGIN DULU (PENTING BANGET)
                    bat "echo %PASS% | docker login -u %USER% --password-stdin"

                    // BUILD
                    bat "docker build -t %USER%/hiburan-backend:latest ./backend"
                    bat "docker build -t %USER%/hiburan-frontend:latest ./frontend"

                    // PUSH
                    bat "docker push %USER%/hiburan-backend:latest"
                    bat "docker push %USER%/hiburan-frontend:latest"
                }
            }
        }

        stage('Deploy to AKS') {
            steps {
                withKubeConfig([credentialsId: 'aks-config']) {

                    bat "kubectl apply -f hiburan-k8s.yaml"
                    bat "kubectl apply -f hiburan-ingress.yaml"

                    bat "kubectl rollout restart deployment backend-hiburan"
                    bat "kubectl rollout restart deployment frontend-hiburan"
                }
            }
        }
    }
}