pipeline {
  agent any

  tools {
    maven 'maven-3'
  }
  environment {
    SONAR_TOKEN = credentials('sonar-token')
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build & Test') {
      steps {
        sh 'mvn clean verify'
      }
    }

    stage('Sonar Analysis') {
      steps {
        withSonarQubeEnv('sonarqube') {
            sh """
            mvn clean verify sonar:sonar \
              -Dsonar.projectKey=ci_pipeline \
              -Dsonar.projectName=tranquangthuan1211_ci_pipeline
            """
        }
      }
    }

    stage('Quality Gate') {
      steps {
        timeout(time: 5, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: true
        }
      }
    }
  }
}
