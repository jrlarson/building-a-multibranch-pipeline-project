pipeline {
    agent {
        docker {
            image 'node:6-alpine'
            args '-p 3000:3000 -p 5000:5000' 
        }
    }
    environment {
        CI = 'true'
        DEPLOY_TARGET = "${env.BRANCH_NAME}"
        CREDENTIALS = 'default'
        DEPLOY_URL = ''
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Deliver for environment') {
            when { anyOf { branch 'development'; branch 'production' } }
            steps {
                script {
                    if( DEPLOY_TARGET == 'development') {
                        CREDENTIALS = 'developer'
                    }
                    if( DEPLOY_TARGET == 'production') {
                        CREDENTIALS = 'producer'
                    }
                    DEPLOY_URL = "https://${CREDENTIALS}.scm.ase1stage.azurenon.nml.com/api/zipdeploy"
                }
                sh "echo $CREDENTIALS"
                sh "echo $DEPLOY_TARGET"
                sh "echo $DEPLOY_URL"
                sh './jenkins/scripts/deploy-for-$DEPLOY_TARGET.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}