import org.jenkinsci.plugins.workflow.steps.FlowInterruptedException
String credentialsId = 'awsCredentials'
try{
	stage('checkout'){
		node{
			cleanWs()
			checkout scm
		}
	}
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
	 environment {
	 	//TF_WORKSPACE = 'Task1' //Sets the Terraform Workspace
	 	//TF_IN_AUTOMATION = 'true'
	 	//AWS_ACCESS_KEY_ID = "${params.AWS_ACCESS_KEY_ID}"
	    	//AWS_SECRET_ACCESS_KEY = "${params.AWS_SECRET_ACCESS_KEY}"
	    //AWS_DEFAULT_REGION = "${params.AWS_REGION}"
	    	PROFILE = "${params.PROFILE}"
	    	ACTION = "${params.ACTION}"
	  }
	stage('Terraform init'){
		node{
 			withCredentials([[
				$class: 'AmazonWebServicesCredentialsBinding',
				credentialsId: credentialsId,
				accessKeyVariable: 'AWS_ACCESS_KEY_ID',
				secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
			]]) {
				ansiColor('xterm') {
					bat 'terraform init'
				}
			}
		}
	}
	if ( env.ACTION == 'plan' ) {
		stage('Terraform plan') {
			node{
				withCredentials([[
					$class: 'AmazonWebServicesCredentialsBinding',
					credentialsId: credentialsId,
					accessKeyVariable: 'AWS_ACCESS_KEY_ID',
					secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
				]]) {
					ansiColor('xterm'){
						//cleanWs()
						git branch: '${params.BRANCH}', url: 'https://github.com/Tavishi123-singh/Jenkins-terraform.git'
						dir("./terraform"){
						bat 'echo "EXECUTING TERRAFORM PLAN !!"'
						bat 'chmod u+x script.sh && ./script.sh'
						bat 'terraform plan'
					}
				}
			}
		}
	}
		
	if ( env.ACTION == 'apply' ) {
		stage('Terraform apply') {
			node{
				withCredentials([[
					$class: 'AmazonWebServicesCredentialsBinding',
					credentialsId: credentialsId,
					accessKeyVariable: 'AWS_ACCESS_KEY_ID',
					secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
				]]) {
					ansiColor('xterm'){
						//cleanWs()
						git branch: '${params.BRANCH}', url: 'https://github.com/Tavishi123-singh/Jenkins-terraform.git'
						dir("./terraform"){
						bat 'echo "EXECUTING TERRAFORM APPLY !!"'
						bat 'chmod u+x script.sh && ./script.sh'
						bat 'terraform apply --auto-approve'
					}
				}
			}
		}
	}
	  currentBuild.result = 'SUCCESS'
}
/*catch (org.jenkinsci.plugins.workflow.steps.FlowInterruptedException flowError) {
  currentBuild.result = 'ABORTED'
}*/
catch (Exception e) {
  currentBuild.result = 'ABORTED'
}
catch (err) {
  currentBuild.result = 'FAILURE'
  throw err
}
finally {
  if (currentBuild.result == 'SUCCESS') {
    currentBuild.result = 'SUCCESS'
  }
}
