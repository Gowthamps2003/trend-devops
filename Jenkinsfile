pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds' // Jenkins Docker Hub credentials ID
        DOCKER_IMAGE = 'gowthamps03/trend-app:latest'
        KUBECONFIG_CREDENTIALS = 'kubeconfig-creds' // Jenkins credentials for kubeconfig if needed
        CLUSTER_NAME = 'trend-devops-eks'
        K8S_NAMESPACE = 'default'
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
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "$DOCKERHUB_CREDENTIALS") {
                        echo "Logged in to Docker Hub"
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "$DOCKERHUB_CREDENTIALS") {
                        sh 'docker push $DOCKER_IMAGE'
                    }
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    // Set kubeconfig if needed via Jenkins credentials
                    withCredentials([file(credentialsId: "$KUBECONFIG_CREDENTIALS", variable: 'KUBECONFIG_FILE')]) {
                        sh 'export KUBECONFIG=$KUBECONFIG_FILE'
                        sh 'kubectl apply -f deployment.yaml'
                        sh 'kubectl apply -f service.yaml'
                        sh 'kubectl rollout status deployment/trend-devops-app-deployment'
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment to EKS successful!'
        }
        failure {
            echo 'Build or Deployment failed!'
        }
    }
}
