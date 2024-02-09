pipeline {
   agent any
   tools {
     maven "maven3.9.6"  
   }
   environment {
        scannerHome = tool "SonarScanner"
               //This can be nexus 3 or Nexus 2
        //NEXUS_VERSION= "nexus"
        //This can be http or https
        //NEXUS_PROTOCOL= "http"
        //Where your Nexus is running
        //NEXUS_URL= "3.6.160.77:8081"
        // Repository Name where we will upload the artifacts
        //NEXUS_REPOSITORY= "Mrsoft-Snapshot-Artifact"
        // Jenkins credentials id to authenticate to Nexus OSS
        //NEXUS_CREDENTIAL_ID= "Nexus"
        AWS_ACCOUNT_ID="547013421517"
        AWS_DEFAULT_REGION="ap-south-1"
        IMAGE_REPO_NAME="mrsoft"
        IMAGE_TAG="${env.BUILD_NUMBER}"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
   }

    stages {
        stage('Git Checkout') {
            steps {
            script{
                git branch: 'dev', credentialsId: 'GitHub', url: 'https://github.com/papunabiswal/mrsoft_dev.git'  
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
        stage('Static code Analisys'){
            steps {
            script{
                def mvn = tool 'maven3.9.6';
                withSonarQubeEnv() {
                    sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=mrsoft"
                }
            }
        }    
            
    }
  //       stage ('Deploying Artifact'){
  //           steps {
  //           script{
  //           sh "mvn deploy"
		// //nexusArtifactUploader artifacts: [[artifactId: 'mrsoft', classifier: '', file: 'target/mrsoft.war', type: 'war']], credentialsId: 'Nexus', groupId: 'in.mrsoft', nexusUrl: 'http://3.6.160.77:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'Mrsoft-Snapshot-Artifact', version: '0.0.1-SNAPSHOT'
  //           }
  //       }
  //   }
        stage('Logging into AWS ECR') {
            steps {
            script {
                sh "aws ecr get-login-password --region ap-south-1 | sudo docker login --username AWS --password-stdin 547013421517.dkr.ecr.ap-south-1.amazonaws.com"
            }
        }
    }
	// Building Docker images
        stage('Building image') {
            steps{
            script {
                dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
            }
        }
    }
    	stage('Pushing to ECR') {
		    steps{
		    script {
                sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
                sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
			}
		  }
		}
        stage('Trigger Update K8s') {
            steps{
            script {
                echo "triggering Update manifest Job"
                build job: 'backendapp-updatek8s', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
            }
        }
    }
   }
}
