pipeline {
  agent any
  options {
    ansiColor('xterm')
    timestamps()
    timeout(time: 10, unit: 'MINUTES')
  }
  
  environment {
    ARTIFACT = "${env.BUILD_NUMBER}.zip"
    SLACK_MESSAGE = "Job ${env.JOB_NAME} Build ${env.BUILD_NUMBER}"
  }


    stages {
      stage('Repository') {
        steps {
           checkout scm
        }
      }
      stage('Test & Build') {
        steps {
          sh "echo ${env.BUILD_NUMBER}"
          sh "echo ${env.WORKSPACE}"
          sh "touch archivo.txt"
          sh "mkdir -p micarpeta"
          sh "touch micarpeta/mifile.txt"
          sh "python main.py"
        }
      }
      stage('Deploy') {
        steps {
          sh "ls -la"
          sh "ls -la micarpeta"
          sh 'echo deploy'
          sh 'echo ${env.SLACK_MESSAGE}'
        }
      }
   }
}

