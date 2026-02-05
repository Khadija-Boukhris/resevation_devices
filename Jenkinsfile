pipeline {
  // Tout le pipeline tourne sur un agent Windows. Si tu préfères,
  // tu peux laisser "any" et ne cibler que le stage Docker avec agent { label 'docker-windows' }.
  agent any
  //agent { label 'docker-windows' }

tools {
    jdk   'jdk17'
    maven 'maven'
  }

  

  environment {
    

    MAVEN_OPTS   = '-Xmx1024m'

    // ----- Azure Container Registry -----
    ACR   = 'acrreservation2.azurecr.io'        // loginServer de l'ACR
    IMAGE = 'reservation-backend'
    TAG   = "${env.BUILD_NUMBER}"
  }

  stages {

    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/zinebmouman/resevation_devices.git'
      }
    }

    stage('Build & Unit Tests (backend)') {
      steps {
        dir('backend') {
          bat 'mvn -B -U clean verify'
        }
      }
      post {
        always {
          junit allowEmptyResults: true, testResults: 'backend/target/surefire-reports/*.xml'
          archiveArtifacts artifacts: 'backend/target/*.jar', fingerprint: true
        }
      }
    }

    

    
  }

  post {
    success {
      echo "Pipeline OK → Image pushed: ${env.ACR}/${env.IMAGE}:${env.TAG}"
    }
    failure { echo 'Pipeline KO' }
  }
}
