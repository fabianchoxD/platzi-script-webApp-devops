pipeline {
  agent any

  options {
    timeout(time: 20, unit: 'MINUTES')
  }

  environment {
    ARTIFACT_ID = "fabiansvaron/webapp:${env.BUILD_NUMBER}"
  }

  stages {
    stage('Build') {
      steps {
        script {
          dir("webapp") {
            dockerImage = docker.build "${env.ARTIFACT_ID}"
          }
        }
      }
    }
    /*stage('Run tests') {
      steps {
        sh "docker run ${dockerImage.id} npm test"
      }
    }*/
    stage('Publish') {
      when {
        branch 'main'
      }
      steps {
        script {
          docker.withRegistry("", "DockerHubCredentials") {
            dockerImage.push()
          }
        }
      }
    }
    stage('Schedule Staging Deployment') {
      when {
        branch 'main'
      }
      steps {
        build job: 'main', parameters: [string(name: 'ARTIFACT_ID', value: "${env.ARTIFACT_ID}")], wait: false
      }
    }
  }
}

