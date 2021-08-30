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
        dockerImage = '' 
    }
  stages {
    stage('Create Version') {
      steps {
        script {
          ARTI_VER = "${env.GIT_RANCH}-${BUILD_NUMBER}"
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
      } stage('Building our image') { 

            steps { 
                script { 
                    dockerImage = docker.build registry + ":$BUILD_NUMBER" 
                }
            } 
        }
        stage('Deploy our image') { 
            steps { 
                script { 
                    docker.withRegistry( '', registryCredential ) { 
                        dockerImage.push() 
                    }
                } 
         }
 	   
             }
        }	    
 }
}
