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
        echo 'Deployement'
        sh 'ng build --prod --output-path /var/www/html/ps6/ --base-href /ps6/'
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'coverage/**/*', fingerprint: true
      echo 'JENKINS PIPELINE'
    }

    success {
      slackSend(
        channel: 'otake',
        failOnError: true,
        color: 'good',
        token: env.SLACK_TOKEN,
        message: 'Job: ' + env.JOB_NAME + ' with buildnumber ' + env.BUILD_NUMBER + ' was successful\n App Deployed.',
        baseUrl: env.SLACK_WEBHOOK)

      echo 'JENKINS PIPELINE SUCCESSFUL'
    }

    failure {
      slackSend(
        channel: 'otake',
        failOnError: true,
        color: 'danger',
        token: env.SLACK_TOKEN,
        message: 'Job: ' + env.JOB_NAME + ' with buildnumber ' + env.BUILD_NUMBER + ' was failed\n ' +
        env.GIT_COMMITTER_NAME + 'has done something wrong',
        baseUrl: env.SLACK_WEBHOOK)

      echo 'JENKINS PIPELINE FAILED'
    }

    unstable {
      slackSend(
        channel: 'otake',
        failOnError: true,
        color: 'warning',
        token: env.SLACK_TOKEN,
        message: 'Job: ' + env.JOB_NAME + ' with buildnumber ' + env.BUILD_NUMBER + ' was unstable',
        baseUrl: env.SLACK_WEBHOOK)

      echo 'JENKINS PIPELINE WAS MARKED AS UNSTABLE'
    }

    changed {
      slackSend(
        channel: 'otake',
        failOnError: true,
        color: 'danger',
        token: env.SLACK_TOKEN,
        message: 'Job: ' + env.JOB_NAME + ' with buildnumber ' + env.BUILD_NUMBER + ' its resulat was unclear',
        baseUrl: env.SLACK_WEBHOOK)

      echo 'JENKINS PIPELINE STATUS HAS CHANGED SINCE LAST EXECUTION'
    }
  }
}
