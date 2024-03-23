pipeline {
   agent any
   tools {
     maven "maven3.9.6"  
   }
   stages {
       stage('Git Checkout') {
            steps {
            script{
                git branch: 'dev', url: 'https://github.com/papunabiswal/mrsoft_dev_backend.git' 
                }  
            }
        }
        stage ('Maven Goal'){
            steps {
            script{
            sh "mvn clean install package"
            } 
        }    
        }
        stage ('Deploying Artifact'){
            steps {
            script{
            sh "mvn deploy"
	       // sh "nexusArtifactUploader credentialsId: 'Nexus', groupId: 'com.backend-dev', nexusUrl: '13.233.155.7:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'Backend-Artifact', version: '0.0.1-SNAPSHOT'"
            }
        }
    }
   }
}
