pipeline {
  agent any

  environment {
    APP_NAME = "spring-security-app"
    IMAGE = "spring-security-app:${BUILD_NUMBER}"
    CONTAINER = "spring-security"
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build Jar') {
      steps {
        sh 'mvn -B clean package -DskipTests'
      }
      post {
        success {
          archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
      }
    }

    stage('Test') {
      steps {
        sh 'mvn -B test'
      }
      post {
        always {
          junit 'target/surefire-reports/*.xml'
        }
      }
    }

    stage('Build Docker Image') {
      steps {
        sh "docker build -t ${IMAGE} ."
      }
    }

    stage('Run Container') {
      steps {
        sh """
          docker rm -f ${CONTAINER} || true
          docker run -d --name ${CONTAINER} -p 8080:8080 ${IMAGE}
        """
      }
    }
  }
}
