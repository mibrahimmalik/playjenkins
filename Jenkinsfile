pipeline {

  environment {
    registry = "kubemaster:5000/justme/myweb"
    dockerImage = ""
  }

  env.MY_BUILD = ":$BUILD_ID" + "$BUILD_NUMBER"

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git 'https://github.com/mibrahimmalik/playjenkins.git'
      }
    }
    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build registry + env.MY_BUILD
        }
      }
    }
    stage('Push Image') {
      steps{
        script {
          docker.withRegistry( "" ) {
            dockerImage.push()
          }
        }
      }
    }

  stage('Remove Unused docker image') {
     steps{
      sh "docker rmi $registry:env.MYBUILD"
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
