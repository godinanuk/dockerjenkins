pipeline {
  environment {
    imagename = "mynat/hello-flask"
    registryCredential = 'docker-hub'
    dockerImage = ''
  }
  agent any
  stages {
    stage('Cloning Git') {
      steps {
        git([url: 'https://github.com/godinanuk/hello-flask.git', branch: 'main'])

      }
    }
    stage('Display user') {
      steps {
        sh 'echo `whoami`'

      }
    }
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build imagename
        }
      }
    }
    stage('Deploy Image') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push("$BUILD_NUMBER")
             dockerImage.push('latest')

          }
        }
      }
    }
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $imagename:$BUILD_NUMBER"
         sh "docker rmi $imagename:latest"

      }
    }
    stage('SonarQube analysis') {
      steps {
        script {
          // requires SonarQube Scanner 2.8+
          scannerHome = tool 'my sonarqube'
        }
        withSonarQubeEnv('my sonarqube') {
          sh "${scannerHome}/bin/sonar-scanner"
        }
      }
    }
    stage("Quality Gate") {
        steps {
            timeout(time: 5, unit: 'MINUTES') {
                // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                // true = set pipeline to UNSTABLE, false = don't
                waitForQualityGate abortPipeline: true
            }
        }
    }
  }
}
