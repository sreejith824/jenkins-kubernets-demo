pipeline {
  environment {
    registry = "sreejith824/apache2-sample"
    registryCredential = "dockerhub"
    dockerImage = ''
    PROJECT_ID = "tcs-cloud-devops"
    CLUSTER_NAME = "jenkins-kubernetes-demo-cluster"
    LOCATION = "us-central1-c"
    CREDENTIALS_ID = "jenkins-kubernetes-demo"    
  }

  agent any
    stages {
    stage('Cloning Git') {
      steps {
        git 'https://github.com/sreejith824/jenkins-kubernets-demo.git'
      }
    }
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Deploy Image') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }

 

    stage('Deploy to GKE') {
      steps{
        step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
      }
    }
  }
  
}