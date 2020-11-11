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
          sh '''
             pwd
             apt-get -qq update && \
             apt-get -qq -y install curl && \
             curl -sSL https://get.docker.com/ | sh && \
             curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl && \
             chmod +x ./kubectl && \
             mv ./kubectl /usr/local/bin/kubectl && \
             curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get | bash
             which docker"
             /var/lib/docker version"
          '''
        script {
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