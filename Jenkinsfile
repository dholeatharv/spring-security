pipeline {
  agent any

  environment {
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
        sh 'chmod +x mvnw'
        sh './mvnw -B clean package -DskipTests'
      }
      post {
        success {
          archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
      }
    }

    stage('Test') {
      steps {
        sh 'chmod +x mvnw'
        sh './mvnw -B test'
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
          docker run -d --name ${CONTAINER} -p 8081:8080 ${IMAGE}
        """
      }
    }
  }
}
