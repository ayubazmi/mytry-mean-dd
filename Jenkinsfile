pipeline {
    agent any

    environment {
        DOCKERHUB_CRED = "dockerhub"
        GITHUB_CRED = "github-token"
    }

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/ayubazmi/mytry-mean-dd.git', 
                    branch: 'main', 
                    credentialsId: env.GITHUB_CRED
            }
        }

        stage('Build Frontend Image') {
            steps {
                sh """
                docker build -t ayubazmi/frontend:${BUILD_NUMBER} ./frontend
                """
            }
        }

        stage('Build Backend Image') {
            steps {
                sh """
                docker build -t ayubazmi/backend:${BUILD_NUMBER} ./backend
                """
            }
        }

        stage('Login & Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: env.DOCKERHUB_CRED,
                    usernameVariable: 'USERNAME',
                    passwordVariable: 'PASSWORD'
                )]) {
                    sh """
                    echo \$PASSWORD | docker login -u \$USERNAME --password-stdin
                    docker push ayubazmi/frontend:${BUILD_NUMBER}
                    docker push ayubazmi/backend:${BUILD_NUMBER}
                    """
                }
            }
        }

        stage('Deploy Locally') {
            steps {
                sh """
                cd /mytry-mean-dd        # path where your docker-compose.yml exists
                docker compose pull
                docker compose up -d
                """
            }
        }
    }

    post {
        success { echo "Deployment successful!" }
        failure { echo "Pipeline failed." }
    }
}
