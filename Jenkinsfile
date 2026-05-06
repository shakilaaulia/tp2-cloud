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

        stage('Build & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-login',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {

                    sh "docker build -t ${USER}/hiburan-backend:latest ./backend"
                    sh "docker build -t ${USER}/hiburan-frontend:latest ./frontend"

                    sh "echo ${PASS} | docker login -u ${USER} --password-stdin"

                    sh "docker push ${USER}/hiburan-backend:latest"
                    sh "docker push ${USER}/hiburan-frontend:latest"
                }
            }
        }

        stage('Deploy to AKS') {
            steps {
                withKubeConfig([credentialsId: 'aks-config']) {

                    sh "kubectl apply -f hiburan-k8s.yaml"
                    sh "kubectl apply -f hiburan-ingress.yaml"

                    sh "kubectl rollout restart deployment backend-hiburan"
                    sh "kubectl rollout restart deployment frontend-hiburan"
                }
            }
        }
    }
}
