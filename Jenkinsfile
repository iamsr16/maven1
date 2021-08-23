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
    stage('Publish test and Code Coverage') {
      steps {
        junit allowEmptyResults: true, skipPublishingChecks: true,
          testResults: 'target/surefire-reports/*.xml'
        publishCoverage adapters: [jacocoAdapter('target/site/jacoco/jacoco*.xml')],
          sourceFileResolver: sourceFiles('NEVER_STORE')
      }
    }
    stage('Local artifact archive') {
      steps {
        archive 'target/app*.war'
      }
    }
  }
