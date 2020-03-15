pipeline {
  environment {
    PROJECT_ID = "tcs-cloud-devops"
    appName = "jenkins-kubernetes-demo"
    imageTag = "gcr.io/${PROJECT_ID}/${appName}:${env.BUILD_NUMBER}"
    CLUSTER_NAME = "jenkins-kubernetes-demo-cluster"
    LOCATION = "us-central1-c"
    CREDENTIALS_ID = "jenkins-kubernetes-demo"
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
        sh "docker build -t $imageTag ."
      }
    }
    stage('Push image to GCR') {
        sh("gcloud docker -- push ${imageTag}")
    }

    stage('Deploy to GKE') {
        steps{
            sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' deployment.yml"
            sh("sed -i.bak 's#gcr.io/${PROJECT_ID}/${appName}:${env.BUILD_NUMBER}#${imageTag}#' *.yml")
                   //Create or update resources
            step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
        }
    }  
  }
}
