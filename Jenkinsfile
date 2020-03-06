pipeline {
  agent any
  stages {
    stage('Deploy App') {
      steps {
        dir(path: '/var/lib/jenkins/workspace/oclim-terraform_master@2/provider/app_stack') {
          sh 'ls -ld $PWD/*'
          sh 'terraform apply -auto-approve'
          sh 'aws elbv2 modify-listener --listener-arn \\"${env.LISTERNERARN}\\" --default-actions \\"[{ "Type": "forward", "Order": 1, "ForwardConfig": { "TargetGroups": [ { "TargetGroupArn": "${env.OLDTGARN}", "Weight": 80 }, { "TargetGroupArn": "${env.NEWTGARN}", "Weight": 20 }, ] } }]\\"'
        }

      }
    }

  }
  environment {
    LISTERNERARN = "${sh(script:'terraform output listener_arn', returnStdout: true)}"
    OLDTGARN = "${sh(script:'terraform output old_tg_arn', returnStdout: true)}"
    NEWTGARN = "${sh(script:'terraform output new_tg_arn', returnStdout: true)}"
  }
  post {
    failure {
      dir(path: '/var/lib/jenkins/workspace/oclim-terraform_master@2/provider/deploy_stack') {
        sh 'pwd'
        sh 'ls -lah'
        sh 'terraform destroy -auto-approve'
      }

    }

    unsuccessful {
      dir(path: '/var/lib/jenkins/workspace/oclim-terraform_master@2/provider/deploy_stack') {
        sh 'pwd'
        sh 'ls -lah'
        sh 'terraform destroy -auto-approve'
      }

    }

    aborted {
      dir(path: '/var/lib/jenkins/workspace/oclim-terraform_master@2/provider/deploy_stack') {
        sh 'pwd'
        sh 'ls -lah'
        sh 'terraform destroy -auto-approve'
      }

    }

  }
}