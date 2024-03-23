pipeline {
   agent any
   tools {
     maven "maven3.9.6"  
   }
 //   environment {
	// //This can be nexus 3 or Nexus 2
 //        NEXUS_VERSION= "nexus3"
 //        //This can be http or https
 //        NEXUS_PROTOCOL= "http"
 //        //Where your Nexus is running
 //        NEXUS_URL= "13.233.155.7:8081"
 //        // Repository Name where we will upload the artifacts
 //        NEXUS_REPOSITORY= "papuna"
 //        // Jenkins credentials id to authenticate to Nexus OSS
 //        NEXUS_CREDENTIAL_ID= "Nexus"
 //   }
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
            }
        }
    }
   }
}
