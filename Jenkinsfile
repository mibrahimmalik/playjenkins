pipeline {

  environment {
    registry = "kubejenacr.azurecr.io/mib/myweb"
    dockerImage = ""
    registryCredential = 'azure_acr'
    MY_BUILD = "$BUILD_ID" + "$BUILD_NUMBER"
  }

  
  agent any

  stages {

    stage("Print ENV Variables") {
      steps {
        sh "printenv"
      }
    }

    stage('Checkout Source') {
      steps {
        git 'https://github.com/mibrahimmalik/playjenkins.git'
      }
    }
    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_ID"
          echo "MY_BUILD = $MY_BUILD"
        }
      }
    }
    stage('Push Image') {
      steps{
        script {
          docker.withRegistry( "" ,registryCredential) {
            dockerImage.push()
          }
        }
      }
    }

  stage('Remove Unused docker image') {
     steps{
      sh "docker rmi $registry:$BUILD_ID"
     }
  }

    stage('Deploy App') {
      steps {
        script {
          kubernetesDeploy(configs: "myweb.yaml", kubeconfigId: "mykubeconfig")
        }
      }
    }
  }
}