pipeline {
   agent any
   tools {
     maven "maven3.9.6"  
   }
   // environment {
   //      // scannerHome = tool "sonar-scanner"
   //      //This can be nexus 3 or Nexus 2
   //      NEXUS_VERSION= "nexus3"
   //      //This can be http or https
   //      NEXUS_PROTOCOL= "http"
   //      //Where your Nexus is running
   //      NEXUS_URL= "13.233.155.7:8081"
   //      // Repository Name where we will upload the artifacts
   //      NEXUS_REPOSITORY= "demo-artifact"
   //      // Jenkins credentials id to authenticate to Nexus OSS
   //      // NEXUS_CREDENTIAL_ID= "Nexus"
   //      // AWS_ACCOUNT_ID="381492085690"
   //      // AWS_DEFAULT_REGION="ap-south-1"
   //      // IMAGE_REPO_NAME="backend-dev"
   //      // IMAGE_TAG="${env.BUILD_NUMBER}"
   //      // REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
   // }

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
    //     stage('Static code Analisys'){
    //         steps {
    //         script{
    //             def mvn = tool 'maven3.9.6';
    //             withSonarQubeEnv() {
    //                 sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=backend-dev"
    //             }
    //         }
    //     }    
            
    // }
        stage ('Deploying Artifact'){
            steps {
            script{
            sh "mvn deploy"
	       // sh "nexusArtifactUploader credentialsId: 'Nexus', groupId: 'com.backend-dev', nexusUrl: '13.233.155.7:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'Backend-Artifact', version: '0.0.1-SNAPSHOT'"
            }
        }
    }
	// stage ('Deploying Artifact'){
 //            steps {
 //              nexusArtifactUploader credentialsId: 'Nexus', groupId: 'com.backend-dev', nexusUrl: '13.233.105.132:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'demo-artifact', version: '0.0.1-SNAPSHOT'
 //        }
 //    }
	    // stage('Upload to Nexus') {
     //        steps {
     //            nexusArtifactUploader(
     //                nexusVersion: 'nexus3',
     //                protocol: 'http',
     //                nexusUrl: 'http://13.233.105.132:8081',
     //                groupId: 'com.backend-dev',
     //                version: '1.0.0',
     //                repository: 'Backend-Artifact',
     //                credentialsId: 'nexus3',
     //                artifacts: [
     //                    [
     //                        artifactId: 'Backend-Artifact',
     //                        // file: 'path/to/your/artifact.jar',
     //                        type: 'war'
     //                    ],
     //                    // Add additional artifacts as needed
     //                ]
     //            )
     //        }
     //    }
        stage('Logging into AWS ECR') {
            steps {
            script {
                sh "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 381492085690.dkr.ecr.ap-south-1.amazonaws.com"
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
    
    stages {
        stage('Clean Workspace') {
            steps {
                // Delete the workspace directory
                deleteDir()
            }
        }
   }
}
