pipeline {
  environment {
    AWS_ACCOUNT_ID="101075526478"
    AWS_DEFAULT_REGION="us-east-1" 
    CLUSTER_NAME="ecs-demo-cluster-test"
    SERVICE_NAME="ecs-demo-service-test"
    TASK_DEFINITION_NAME="ecs-terraform-demo"
    DESIRED_COUNT="4"
    IMAGE_REPO_NAME="ecs-demo-test"
    IMAGE_TAG="${env.BUILD_ID}"
    REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    registryCredential = "demo-admin-user"

    MAIL_TO ='anshulv1401@gmail.com'
  }
  agent {label 'backendapi-java'}
  stages {
	stage('Maven Build') {
      steps{
        sh 'echo inside maven build'
        sh 'ls -al'
        dir("${env.WORKSPACE}"){
        sh 'echo inside dir'
        sh "pwd"
        sh 'mvn clean install'
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

  // Uploading Docker images into AWS ECR
  stage('Pushing to ECR') {
    steps{  
      script {
        docker.withRegistry("https://" + REPOSITORY_URI, "ecr:${AWS_DEFAULT_REGION}:" + registryCredential) {
            dockerImage.push()
        }
      }
    }
  }

  stage('Deploy') {
    steps{
      withAWS(credentials: registryCredential, region: "${AWS_DEFAULT_REGION}") {
        script {
              sh './script.sh'
            }
        } 
      }
    }
  }
	post{
        always{
	    mail to:"${MAIL_TO}",
            subject: "Capstone Project-Bankend API-jenkins build:${currentBuild.currentResult}: ${env.JOB_NAME}",
            body: "${currentBuild.currentResult}: Job ${env.JOB_NAME}\n More Info can be found here: ${env.BUILD_URL}"
	   /*emailext to: "kesavannarayanan@gmail.com",
            subject: "Capston Project-Bankend API-jenkins build:${currentBuild.currentResult}: ${env.JOB_NAME}",
            body: "${currentBuild.currentResult}: Job ${env.JOB_NAME}\n More Info can be found here: ${env.BUILD_URL}"*/
        }
    }
 }
