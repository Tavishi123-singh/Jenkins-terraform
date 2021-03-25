pipeline {
  agent any
  parameters {
		choice (name: 'ACTION',
				choices: [ 'plan', 'apply'],
				description: 'Run terraform plan / apply')
		string (name: 'PROFILE',
			   defaultValue: 'myprofile',
			   description: 'Optional. Target aws profile defaults to myprofile')
	        gitParameter branchFilter: 'origin/(.*)', defaultValue: '', name: 'BRANCH', type: 'PT_BRANCH'
	  	//password (name: 'AWS_ACCESS_KEY_ID')
    		//password (name: 'AWS_SECRET_ACCESS_KEY')	
    }
 /* environment {
    TF_WORKSPACE = 'Task1' //Sets the Terraform Workspace
    TF_IN_AUTOMATION = 'true'
    AWS_ACCESS_KEY_ID = "${params.AWS_ACCESS_KEY_ID}"
    AWS_SECRET_ACCESS_KEY = "${params.AWS_SECRET_ACCESS_KEY}"
    //AWS_DEFAULT_REGION = "${params.AWS_REGION}"
    PROFILE = "${params.PROFILE}"
    ACTION = "${params.ACTION}"
  }*/
	stages {
		stage('Terraform plan') {
			when { expression { ACTION == 'plan' } }
			steps {
				cleanWs()
				git branch: '${params.BRANCH}', url: 'https://github.com/Tavishi123-singh/Jenkins-terraform.git'
				dir("./terraform"){
				sh 'echo "EXECUTING TERRAFORM PLAN !!"'
				sh 'chmod u+x script.sh && ./script.sh'
				sh 'terrfaorm init  && terraform plan'
				
				}
			}
		}
		stage('terraform apply') {
			when { expression { ACTION == 'apply' } }
			steps {
				cleanWs()
				git branch: '${params.BRANCH}', url: 'https://github.com/Tavishi123-singh/Jenkins-terraform.git'
				dir("./terraform") {
				sh 'echo "EXECUTING TERRAFORM APPLY !!"'
				sh 'chmod u+x script.sh && ./script.sh'
				sh 'terrfaorm init  && terraform apply --auto-approve'
				}
			}
		}
	}
}
