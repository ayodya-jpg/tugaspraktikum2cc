pipeline {
  agent any
  environment {
    IMAGE_NAME = 'ayodyawasesa/simple-app'
    REGISTRY_CREDENTIALS = 'dockerhub-credentials'
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build') {
      steps {
        bat 'echo "Build di Windows dimulai..."'
      }
    }

    stage('Unit Test') {
      steps {
        bat 'echo "Menjalankan Unit Test..."'
      }
    }

    stage('Build Docker Image') {
      steps {
        bat """docker build -t ${env.IMAGE_NAME}:${env.BUILD_NUMBER} ."""
      }
    }

    stage('Push Docker Image') {
      steps {
        withCredentials([usernamePassword(credentialsId: env.REGISTRY_CREDENTIALS, usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          bat """docker login -u %USER% -p %PASS%"""
          bat """docker push ${env.IMAGE_NAME}:${env.BUILD_NUMBER}"""
          bat """docker tag ${env.IMAGE_NAME}:${env.BUILD_NUMBER} ${env.IMAGE_NAME}:latest"""
          bat """docker push ${env.IMAGE_NAME}:latest"""
        }
      }
    }
  }
  post {
    always {
      echo 'Pipeline selesai dijalankan.'
    }
  }
}


