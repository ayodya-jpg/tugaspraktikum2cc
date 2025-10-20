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
        bat '''
        echo "Menjalankan Unit Test..."
        python -m pip install --upgrade pip
        pip install -r requirements.txt
        pip install pytest
        pytest --maxfail=1 --disable-warnings -q
        '''
      }
    }
    stage('Build Docker Image') {
      when {
        expression {
          // Jalankan build hanya jika stage Unit Test sukses
          currentBuild.resultIsBetterOrEqualTo('SUCCESS')
        }
      }
      steps {
        bat """docker build -t ${env.IMAGE_NAME}:${env.BUILD_NUMBER} ."""
      }
    }
    stage('Push Docker Image') {
      when {
        expression {
          // Jalankan push hanya jika stage Unit Test dan Build sukses
          currentBuild.resultIsBetterOrEqualTo('SUCCESS')
        }
      }
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
