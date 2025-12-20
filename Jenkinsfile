pipeline {
  agent any

  tools {
    maven 'maven-3'
    nodejs 'Node_23'
  }
  environment {
    SONAR_TOKEN = credentials('sonar')
    SONAR_ORG = 'tranquangthuan1211'
    SONAR_PROJECT_KEY = 'tranquangthuan1211_ci_pipeline'
    SONAR_PROJECT_NAME = 'ci_pipeline'
    SNYK_TOKEN = credentials('snyk-token')
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
    stage('Install Snyk CLI') {
        steps {
            sh 'npm install -g snyk'
        }
    }
    stage('Snyk Dependency Scan') {
      steps {
        sh ''' snyk auth $SNYK_TOKEN
               snyk test --severity-threshold=high
        '''
      }
    }
    stage('Sonar Analysis') {
          steps {
            withSonarQubeEnv('sonarcloud') {
                sh """
                        mvn sonar:sonar \
                          -Dsonar.organization=${SONAR_ORG} \
                          -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                          -Dsonar.projectName=${SONAR_PROJECT_NAME}
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
