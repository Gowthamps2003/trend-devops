pipeline {
  agent any

  stages {
    stage('Clone') {
      steps {
        git 'https://github.com/Gowthamps2003/trend-devops.git'
      }
    }

    stage('Build') {
      steps {
        sh 'docker build -t gowthamps03/trend-app:latest .'
      }
    }

    stage('Push') {
      steps {
        withDockerRegistry([ credentialsId: 'docker-hub-creds', url: '' ]) {
            sh 'docker push gowthamps03/trend-app:latest'
        }
      }
    }
  }
}
