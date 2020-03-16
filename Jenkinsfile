pipeline {
  environment {

    dockerImage = ''
    PROJECT_ID = "tcs-cloud-devops"
    CLUSTER_NAME = "jenkins-kubernetes-demo-cluster"
    LOCATION = "us-central1-c"
    CREDENTIALS_ID = "jenkins-kubernetes-demo"  
    dockerImageTag = "gcr.io/tcs-cloud-devops/demo-apache2-kube"  
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
          dockerImage = docker.build dockerImageTag
        }
      }
    }
    stage('Publish Image to GCR') {
      steps{
        sh "docker push $dockerImage"
      }
    }

    stage('Deploy to GKE') {
      steps{
        sh "sed -i 's#gcr.io/tcs-cloud-devops/demo-apache2-kube@${env.BUILD_ID}#gcr.io/tcs-cloud-devops/demo-apache2-kube:latest#' deployment.yml"
        step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
      }
    }
  }
  
}