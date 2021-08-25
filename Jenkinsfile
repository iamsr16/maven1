def ARTI_VER
def appImage
pipeline {
  agent any
  tools {
    maven 'Maven 3.6'
  }
  stages {
    stage('Create Version') {
      steps {
        script {
          ARTI_VER = "${env.BRANCH_NAME}-${BUILD_NUMBER}"
          echo "${ARTI_VER}"
        }
      }
    }
    stage('Build') {
      steps {
		script {
          sh "mvn -s settings.xml -Drevision=$ARTI_VER clean package"
        }
      }
    }
   stage('Deploy to nexus') {
      steps {
        script {
          sh "mvn -s settings.xml -Drevision=$ARTI_VER deploy"
        }
      }
    }
  }	    
  post {
    success {
      office365ConnectorSend color: '#00cc00', message: "Success  ${JOB_NAME} build_number:${BUILD_NUMBER}, branch:${env.BRANCH_NAME} url:(<${BUILD_URL}>)", status: 'SUCCESS', webhookUrl: "${TEAMS_WEBHOOK}"
    }
    failure {
      office365ConnectorSend color: '#fc2c03', message: "Failed  ${JOB_NAME} build_number:${BUILD_NUMBER}, branch:${env.BRANCH_NAME} url:(<${BUILD_URL}>)", status: 'FAILED', webhookUrl:"${TEAMS_WEBHOOK}"
    }
   }
 }

