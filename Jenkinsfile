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
        DEPLOY_TYPE = 'default'
        DEPLOY_URL = ''
        CREDENTIAL_ID_DEVELOP = 'PIPELINE_PROJ'
        CREDENTIAL_ID_PROD = 'PIPELINE_PROJ_PROD'
        CREDENTIALS = credentials("${CREDENTIAL_ID_DEVELOP}")
    }
    stages {
        stage('Initialize Deployment Parameters') {
            steps {
                script {
                    if( DEPLOY_TARGET == 'development') {
                        DEPLOY_TYPE = 'developer'
                    }
                    if( DEPLOY_TARGET == 'production') {
                        DEPLOY_TYPE = 'producer'
                        CREDENTIALS = credentials("${CREDENTIAL_ID_PROD}")
                    }
                    DEPLOY_URL = "https://${DEPLOY_TYPE}.scm.ase1stage.azurenon.nml.com/api/zipdeploy"
                    lastAPIBuild = "<number>867742</number>"
                    firstChar = lastAPIBuild.indexOf('>')
                    lastChar = lastAPIBuild.lastIndexOf('<')
                    rawBuildNum = lastAPIBuild.substring(firstChar+1,lastChar)
                    echo rawBuildNum
                }
            }
        }
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
                sh "echo $DEPLOY_TYPE"
                sh "echo $DEPLOY_TARGET"
                sh "echo $DEPLOY_URL"
                sh "echo $CREDENTIALS_USR"
                sh "echo $CREDENTIALS_PSW"
                sh './jenkins/scripts/deploy-for-$DEPLOY_TARGET.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}