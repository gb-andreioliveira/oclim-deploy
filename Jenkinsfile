
def getCurrentInstance(String asgGroupName){
    String strInstanceIds = sh(
        script: "aws autoscaling describe-auto-scaling-instances --region us-east-1  | jq -r '.AutoScalingInstances[] | select( .AutoScalingGroupName == \"$asgGroupName\") | .InstanceId'",
        returnStatus: true
    )

    return strInstanceIds.split("\n")
}

String asgGroupName =  "oclim-app-terraform"

def asgInitialInstances = []


pipeline {
    agent any
    stages {
        stage('Get Blue Instances') {
            steps {
                dir(path: '/var/lib/jenkins/jobs/oclim-terraform/branches/master/workspace@2/provider/app_stack') {
                    script {
                        asgInitialInstances = getCurrentInstance(asgGroupName)
                    }
                }
            }
        }
        stage('Scale-out Green Instances') {
            steps {
                dir(path: '/var/lib/jenkins/jobs/oclim-terraform/branches/master/workspace@2/provider/app_stack') {
                    sh 'pwd'
                    sh 'ls -lah'
                    script {
                        def instances = 5
                        for (instances = 5; instances < 9; instances++) {
                            def green = Math.round(((instances-4)/instances)*100)
                            def blue = Math.round(((instances-(instances-4))/instances)*100)

                            sh "aws autoscaling set-desired-capacity --auto-scaling-group-name `terraform output asg_arn` --desired-capacity ${instances} --region us-east-1 --no-honor-cooldown"
                            sh "aws autoscaling describe-auto-scaling-groups --auto-scaling-group-name `terraform output asg_arn` --region us-east-1"
                            input "${blue}% blue / ${green}% green environment. Would you like to continue or abort?"
                        }
                    }
                }
            }
        }
        stage('Scale-in Blue Instances') {
            steps {
                dir(path: '/var/lib/jenkins/jobs/oclim-terraform/branches/master/workspace@2/provider/app_stack') {
                    script {
                        for (int instances = 0; instances < 4; instances++) {
                            def blue = Math.round(4-instances)/(8-instances)
                            def green = Math.round(((4+instances)/8)*100)
                            sh "aws autoscaling detach-instances --instance-ids ${asgInitialInstances[instances]} --auto-scaling-group-name `terraform output asg_arn` --region us-east-1"
                            sh "aws ec2 terminate-instances --instance-ids ${asgInitialInstances[instances]} --region us-east-1"
                            input "${blue}% blue / ${green}% green environment. Would you like to continue or abort?"
                        }
                    }
                    echo 'Scale-in Complete: All Blue Instances detached and terminated.'
                    /*sh "aws autoscaling set-desired-capacity --auto-scaling-group-name `terraform output asg_arn` --desired-capacity 4 --region us-east-1"
                    sh "aws autoscaling describe-auto-scaling-groups --auto-scaling-group-name `terraform output asg_arn` --region us-east-1"
                    sh 'echo "Decomissioned Blue environment instances."'*/
                }
            }
        }
    }
    post {
        aborted {
            dir(path: '/var/lib/jenkins/jobs/oclim-terraform/branches/master/workspace@2/provider/app_stack') {
                sh "aws autoscaling set-desired-capacity --auto-scaling-group-name `terraform output asg_arn` --desired-capacity 4 --region us-east-1"
                sh "aws autoscaling describe-auto-scaling-groups --auto-scaling-group-name `terraform output asg_arn` --region us-east-1"
                sh 'echo "Group has scaled back to original size"'
            }
        }
        failure {
            dir(path: '/var/lib/jenkins/jobs/oclim-terraform/branches/master/workspace@2/provider/app_stack') {
                sh "aws autoscaling set-desired-capacity --auto-scaling-group-name `terraform output asg_arn` --desired-capacity 4 --region us-east-1"
                sh "aws autoscaling describe-auto-scaling-groups --auto-scaling-group-name `terraform output asg_arn` --region us-east-1"
                sh 'echo "Group has scaled back to original size"'
            }
        }
    }
}
