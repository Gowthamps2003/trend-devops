pipeline {
    agent any

    environment {
        DOCKER_HUB_USERNAME = 'gowthamps03'
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-creds') // create this credential in Jenkins
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Gowthamps2003/trend-devops.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_HUB_USERNAME}/trend-app:latest ."
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    sh "echo ${DOCKER_HUB_CREDENTIALS_PSW} | docker login -u ${DOCKER_HUB_USERNAME} --password-stdin"
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    sh "docker push ${DOCKER_HUB_USERNAME}/trend-app:latest"
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    sh "kubectl apply -f deployment.yaml"
                    sh "kubectl apply -f service.yaml"
                }
            }
        }
    }

    post {
        success {
            echo "Build, Push, and Deployment to EKS completed successfully!"
        }
        failure {
            echo "Build or Deployment failed!"
        }
    }
}
