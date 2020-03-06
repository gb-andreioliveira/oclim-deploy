pipeline {
  agent any
  environment {
      ASGARN = "${sh(script:'terraform output asg_arn', returnStdout: true)}"
    }
    stages {
    stage('Deploy App') {
      steps {
        dir(path: '/var/lib/jenkins/workspace/oclim-terraform_master@2/provider/deploy_stack') {
          sh 'ls -lah'
          sh "aws autoscaling set-desired-capacity --auto-scaling-group-name ${env.ASGARN} --desired-capacity 5"
          sh "aws autoscaling describe-auto-scaling-groups --auto-scaling-group-name ${env.ASGARN}"
          input('Would you like to continue or abort?')
          sh 'ls -lah'
          sh "aws autoscaling set-desired-capacity --auto-scaling-group-name ${env.ASGARN} --desired-capacity 6"
          sh "aws autoscaling describe-auto-scaling-groups --auto-scaling-group-name ${env.ASGARN}"
          input('Would you like to continue or abort?')
          sh 'ls -lah'
          sh "aws autoscaling set-desired-capacity --auto-scaling-group-name ${env.ASGARN} --desired-capacity 7"
          sh "aws autoscaling describe-auto-scaling-groups --auto-scaling-group-name ${env.ASGARN}"
          input('Would you like to continue or abort?')
          sh 'ls -lah'
          sh "aws autoscaling set-desired-capacity --auto-scaling-group-name ${env.ASGARN} --desired-capacity 8"
          sh "aws autoscaling describe-auto-scaling-groups --auto-scaling-group-name ${env.ASGARN}"
          }
        }
      }
    }
  post {
    aborted {
        sh 'ls -lah'
        sh "aws autoscaling set-desired-capacity --auto-scaling-group-name ${env.ASGARN} --desired-capacity 4"
        sh "aws autoscaling describe-auto-scaling-groups --auto-scaling-group-name ${env.ASGARN}"
        sh 'echo "Group has scaled back to original size"'
    }
    failure {
            sh 'ls -lah'
            sh "aws autoscaling set-desired-capacity --auto-scaling-group-name ${env.ASGARN} --desired-capacity 4"
            sh "aws autoscaling describe-auto-scaling-groups --auto-scaling-group-name ${env.ASGARN}"
            sh 'echo "Group has scaled back to original size"'
        }
  }
}