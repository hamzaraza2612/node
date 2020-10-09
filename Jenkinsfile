pipeline {
  agent any
  environment {
    registry = "hamza2612/test"
    dockerImage = ""
    Deploy = "true"
  }
  stages {
    stage('Poll SCM') {
      steps {
        cleanWs()
        checkout scm
      }
    }
    stage('Build Image') {
      steps {
        script {
          dockerImage = docker.build("${registry}:${BUILD_NUMBER}", "node-test") 
        }
      }
    }
    stage('Push Image') {
      steps {
        script {
          docker.withRegistry('', 'docker_hub_reg') {
            dockerImage.push()
          }
        }
      }
    }
    stage('Deploy') {
      when {
        expression { Deploy == "true" }
      }
      steps {
        script {
          sh 'sudo microk8s kubectl create -f webapp-deployment.yaml'
          sh 'sudo microk8s kubectl create -f webapp-service.yaml'
          sh 'sudo microk8s kubectl get all'
        }
      }
    }
    stage('Update') {
      when {
          expression { Deploy == "false" }
        }
      steps {
        script {
          sh "sudo microk8s kubectl set image deployment.apps/webapp webapp=${registry}:${BUILD_NUMBER}"
        }
      }
    }
  }
}
