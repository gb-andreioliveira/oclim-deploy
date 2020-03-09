pipeline {
  agent any
  stages {
    stage('Deploy App') {
      steps {
        dir(path: '/var/lib/jenkins/workspace/oclim-terraform_master@2/provider/app_stack') {
          for (int instances = 5; instances < 9; instances++) {
            def blue = 100-(100/i)
            def green = (100/i)
            sh 'aws autoscaling set-desired-capacity --auto-scaling-group-name `terraform output asg_arn` --desired-capacity ${instances} --region us-east-1'
            sh "aws autoscaling describe-auto-scaling-groups --auto-scaling-group-name `terraform output asg_arn` --region us-east-1"
            input '${blue}% blue / ${green}% green environment. Would you like to continue or abort?'
          }
          sh "aws autoscaling set-desired-capacity --auto-scaling-group-name `terraform output asg_arn` --desired-capacity 4 --region us-east-1"
          sh "aws autoscaling describe-auto-scaling-groups --auto-scaling-group-name `terraform output asg_arn` --region us-east-1"
          sh 'echo "New environment setup sucessfully. Old instances are going to be decomissioned."'
        }
      }
    }
  }
  environment {
    ASGARN = "oclim-terraform-app"
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