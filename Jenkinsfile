pipeline {

  agent {
    label 'docker-build'
  }

  options { 
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }

  environment {
    REPO_NAME = "eclipsecbi"
    DOCKERTOOLS_PATH = sh(script: "printf ${env.WORKSPACE}/.dockertools", returnStdout: true)
  }

  triggers {
    cron('H H * * */3')
  }

  stages {
    stage('Build and push JIRO agent images') {
      steps {
        withDockerRegistry([credentialsId: 'e93ba8f9-59fc-4fe4-a9a7-9a8bd60c17d9', url: 'https://index.docker.io/v1/']) {
          sh 'make all'
        }
      }
    }
  }

  post {
    failure {
      mail to: 'releng-team@eclipse-foundation.org',
        subject: "[CBI] Build Failure ${currentBuild.fullDisplayName}",
        mimeType: 'text/html',
        body: "Project: ${env.JOB_NAME}<br/>Build Number: ${env.BUILD_NUMBER}<br/>Build URL: ${env.BUILD_URL}<br/>Console: ${env.BUILD_URL}/console"
    }
    fixed {
      mail to: 'releng-team@eclipse-foundation.org',
        subject: "[CBI] Back to normal ${currentBuild.fullDisplayName}",
        mimeType: 'text/html',
        body: "Project: ${env.JOB_NAME}<br/>Build Number: ${env.BUILD_NUMBER}<br/>Build URL: ${env.BUILD_URL}<br/>Console: ${env.BUILD_URL}/console"
    }
    cleanup {
      deleteDir() /* clean up workspace */
    }
  }
}
