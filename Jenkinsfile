#!/usr/bin/env groovy

pipeline {
  agent any
  options {
    disableConcurrentBuilds()
    timeout(time: 1, unit: 'HOURS')
  }
  stages {
    stage('Checkout') {
      steps {
        echo 'Checkout'
      }
    }
    stage('Install') {
      steps {
        echo 'Install Dependencies'
        sh 'npm install'
      }
    }
    stage('Lint') {
      steps {
        echo 'Typescript Linter'
        sh 'npm run lint'
      }
    }
    stage('Test') {
      steps {
        echo 'Karma Test'
        sh 'npm run coverage'
      }
    }
    stage('Sonar') {
      steps {
        echo 'Sonar Analysis'
        withSonarQubeEnv('Sonarqube_env'){
          sh 'npm run sonar'
        }
      }
    }
    stage('Quality Gate') {
      steps {
        timeout(time: 1, unit: 'HOURS') {
          waitForQualityGate true
        }
      }
    }
    stage('Deployment'){
      when {
        expression { env.GIT_BRANCH == 'master' }
      }
      steps {
        echo 'TODO'
        echo 'Hello, master!'
      }
    }
    stage('Done') {
      steps {
        echo 'Done'
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'front/coverage/**/*', fingerprint: true
      echo 'JENKINS PIPELINE'
    }

    success {
      echo 'JENKINS PIPELINE SUCCESSFUL'
    }

    failure {
      echo 'JENKINS PIPELINE FAILED'
    }

    unstable {
      echo 'JENKINS PIPELINE WAS MARKED AS UNSTABLE'
    }

    changed {
      echo 'JENKINS PIPELINE STATUS HAS CHANGED SINCE LAST EXECUTION'
    }
  }
}
