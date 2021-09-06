def ARTI_VER
def appImage
pipeline {
  agent any
  tools {
    maven 'Maven 3.6'
  }
	environment { 
        registry = "shreyaa1605/mavenrepo" 
        registryCredential = 'shreyaa1605' 
        dockering = '' 
    }
  stages {
    stage('Create Version') {
      steps {
        script {
          ARTI_VER = "${env.GIT_BRANCH}-${BUILD_NUMBER}"
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
        nexusArtifactUploader artifacts: [
		[
			artifactId: 'maven1',
			classifier: '',
			file: 'target/maven1-0.0.1-SNAPSHOT.jar', 
			type: 'jar'
		]
	], 
		credentialsId: 'nexus3', 
		groupId: 'com.task', 
		nexusUrl: '192.168.43.76:8081', 
		nexusVersion: 'nexus3', 
		protocol: 'http', 
		repository: 'Demorepo', version: '0.0.1-SNAPSHOT'
        }
      } 	   
   }
 stage('Building docker image') {
		   steps {
			   script {
				    dockering = docker.build registry + ":$BUILD_NUMBER"
			   }
		   }
	   }
	   stage ('Deploy docker image') {
		   steps {
			   script { 
				   docker.withRegistry('', registryCredential )
				   {
					   dockering.push("${env.BUILD_NUMBER}")
					   dockering.push("latest")
				   }
		   }
 	   
             }
        }
	 
	   stage('Docker image scan') {
		   steps {
			   script {
				      sh '''
				      docker run -d --name db arminc/clair_new
        sleep 15 # wait for db to come up
        docker run -p 6060:6060 --link db:postgres -d --name clair arminc/clair-local-scan
        sleep 1
        DOCKER_GATEWAY=$(docker network inspect bridge --format "{{range .IPAM.Config}}{{.Gateway}}{{end}}")
        wget -qO clair-scanner https://github.com/arminc/clair-scanner/releases/download/v8/clair-scanner_linux_amd64 && chmod +x clair-scanner
        ./clair-scanner --ip="$DOCKER_GATEWAY" myapp:latest || exit 0
	  '''
			   }
		   }
	   }
    }
    post {
    success {
      office365ConnectorSend color: '#00cc00', message: "Success  ${JOB_NAME} build_number:${env.BUILD_NUMBER}, branch:${env.GIT_BRANCH} url:(<${BUILD_URL}>)", status: 'SUCCESS', webhookUrl: "${env.TEAMS_WEBHOOK}"
    }
    failure {
      office365ConnectorSend color: '#fc2c03', message: "Failed  ${JOB_NAME} build_number:${env.BUILD_NUMBER}, branch:${env.GIT_BRANCH} url:(<${BUILD_URL}>)", status: 'FAILED', webhookUrl:"${env.TEAMS_WEBHOOK}"
    }
  }
}
