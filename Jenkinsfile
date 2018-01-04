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
                echo '${DEPLOY_TARGET}'
                sh './jenkins/scripts/deliver-for-$DEPLOY_TARGET.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}