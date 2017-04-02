#!/usr/bin/env groovy

node('master') {
    try {
        stage('build') {
            git url: 'git@github.com:codebasement/ticketbeast.git'

            // Start services (Let docker-compose build containers for testing)
            sh "./dev up -d"

            // Get composer dependencies
            sh "./dev composer install"

            // Create .env file for testing
            sh '/var/lib/jenkins/.venv/bin/aws s3 cp s3://ticketbeast-secrets/env-ci .env'
            sh './dev art key:generate'
        }

        stage('test') {
            sh "APP_ENV=testing ./dev test"
        }

        if( env.BRANCH_NAME == 'master') {
            stage('package') {
                sh './docker/build'
            }
        }

    } catch(error) {
      // Maybe some alerting
      throw error
    } finally {
        // Spindown containers no matter what happens
        sh './dev down'
    }
}