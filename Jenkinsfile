pipeline {
  agent any
  options {
    ansiColor('xterm')
    timestamps()
    timeout(time: 10, unit: 'MINUTES')
  }
  
  environment {
    ARTIFACT = "${env.BUILD_NUMBER}.zip"
    SLACK_MESSAGE = "Job ${env.JOB_NAME} Build ${env.BUILD_NUMBER} URL ${env.BUILD_URL}"
  }

  parameters {
    string(name: 'SLACK_CHANNEL', defaultValue: '#deploys', description: '')
    choice(name: 'TYPE', choices: 'aut\ncron\ndata', description: 'Autoscaling, Cron or Data')
    booleanParam(name: 'LC', defaultValue: false, description: 'Update aws launch configuration with the new ami')
  }



    stages {
      stage('Repository') {
        steps {
           checkout scm
        }
      }
      stage('Test') {
        steps {
          parallel (
            sintaxis: { sh "echo check_syntax" },
            var_dump: { sh "echo grep_var_dump" }
          )
        }
      }

      stage('Build') {
        steps {
          sh "echo ${env.BUILD_NUMBER}"
          sh "echo ${env.WORKSPACE}"
          sh "touch archivo.txt"
          sh "mkdir -p micarpeta"
          sh "touch micarpeta/mifile.txt"
          sh "python main.py"
          script {
            def ID = sh(returnStdout: true, script: "./ami_id.sh ${env.BUILD_NUMBER}").trim()
            sh "./build_ami.sh ${ID}"
          }
          echo "${env.SLACK_MESSAGE}"
          echo "${params.SLACK_CHANNEL}"
          echo "${params.TYPE}"
          echo "${params.LC}"
        }
      }
      stage('Deploy') {
        when {
          expression {
            return params.LC ==~ /(?i)(Y|YES|T|TRUE|ON|RUN)/
          }
        }
        steps {
          sh "echo hagamos_deploy"
          build job: "pipeline-prod", parameters: [
            [$class: 'StringParameterValue', name: 'VPC_ID', value: 'vpc-123'],
            [$class: 'StringParameterValue', name: 'SLACK', value: '#deploys']
          ]
        }
      }
   }
}

