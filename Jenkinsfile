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
        withSonarQubeEnv('sonarcloud') {
            sh """
                    mvn sonar:sonar \
                      -Dsonar.organization=tranquangthuan1211 \
                      -Dsonar.projectKey=tranquangthuan1211_ci_pipeline \
                      -Dsonar.projectName=ci_pipeline
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
