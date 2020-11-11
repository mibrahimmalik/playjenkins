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
          echo "MY_BUILD = $MY_BUILD"
          sh "pwd"
          sh "which docker"
          sh "/var/lib/docker version"
          sh "hostname"
          sh  "pwd"
          sh "apt-get -qq update"
          sh "apt-get -qq -y install curl"
          sh  "curl -sSL https://get.docker.com/ | sh "
      
          dockerImage = docker.build registry + ":$BUILD_ID"
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