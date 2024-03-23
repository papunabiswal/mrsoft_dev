pipeline {
   agent any
   tools {
     maven "maven3.9.6"  
   }
   environment {
	//This can be nexus 3 or Nexus 2
        NEXUS_VERSION= "nexus3"
        //This can be http or https
        NEXUS_PROTOCOL= "http"
        //Where your Nexus is running
        NEXUS_URL= "13.233.155.7:8081"
        // Repository Name where we will upload the artifacts
        NEXUS_REPOSITORY= "papuna"
        // Jenkins credentials id to authenticate to Nexus OSS
        NEXUS_CREDENTIAL_ID= "Nexus"
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
	stage('Static code Analisys'){
            steps {
            script{
                def mvn = tool 'maven3.9.6';
                withSonarQubeEnv() {
                    sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=backend-dev"
                }
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
    stage('Logging into AWS ECR') {
            steps {
            script {
                sh "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 381492085690.dkr.ecr.ap-south-1.amazonaws.com"
            }
        }
    }
    stage('Building image') {
            steps {
            script {
                dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
            }
        }
    }
    stage('Pushing to ECR') {
            steps {
            script {
                sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
		sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
            }
        }
    }
    stage('Trigger Update K8s') {
            steps {
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
}
