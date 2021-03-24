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
		
    }
	stages {
		stage('Terraform plan') {
			when { expression { ACTION == 'plan' } }
			steps {
				cleanWs()
				git branch: "${params.BRANCH}", url: 'https://github.com/Tavishi123-singh/Jenkins-terraform.git'
				dir("./terraform"){
				sh 'echo "EXECUTING TERRAFORM PLAN !!"'
				sh 'chmod u+x script.sh && ./script.sh'
				//sh 'terrfaorm init -backend-config="conn_str=postgres://postgres"'
				}
			}
		}
		stage('terraform apply') {
			when { anyOf
					{
						environment name: 'ACTION', value: 'apply'
					}
				}
			steps {
				dir("${PROJECT_DIR}") {
					script {
						wrap([$class: 'AnsiColorBuildWrapper', colorMapName: 'xterm']) {
							withCredentials([
								[ $class: 'AmazonWebServicesCredentialsBinding',
									accessKeyVariable: 'AWS_ACCESS_KEY_ID',
									secretKeyVariable: 'AWS_SECRET_ACCESS_KEY',
									//credentialsId: 'myprofile-AWS-access',
									]])
								{
								try {
									tfCmd('apply', 'tfplan')
								} catch (ex) {
                  currentBuild.result = "UNSTABLE"
								}
							}
						}
					}
				}
			}
			post {
				always {
					archiveArtifacts artifacts: "keys/key-${ENV_NAME}.*", fingerprint: true
					archiveArtifacts artifacts: "main/show-${ENV_NAME}.txt", fingerprint: true
				}
			}
		}
		stage('terraform destroy') {    
			when { anyOf
					{
						environment name: 'ACTION', value: 'destroy';
					}
				}
			steps {
				script {
					def IS_APPROVED = input(
						message: "Destroy ${ENV_NAME} !?!",
						ok: "Yes",
						parameters: [
							string(name: 'IS_APPROVED', defaultValue: 'No', description: 'Think again!!!')
						]
					)
					if (IS_APPROVED != 'Yes') {
						currentBuild.result = "ABORTED"
						error "User cancelled"
					}
				}
				dir("${PROJECT_DIR}") {
					script {
						wrap([$class: 'AnsiColorBuildWrapper', colorMapName: 'xterm']) {
							withCredentials([
								[ $class: 'AmazonWebServicesCredentialsBinding',
									accessKeyVariable: 'AWS_ACCESS_KEY_ID',
									secretKeyVariable: 'AWS_SECRET_ACCESS_KEY',
									//credentialsId: 'myprofile-AWS-access',
									]])
								{
								try {
									tfCmd('destroy', '-auto-approve')
								} catch (ex) {
									currentBuild.result = "UNSTABLE"
								}
							}
						}
					}
				}
			}
		}	
  	}
  /*post
    {
			always{
			emailext (
			body: """
				<p>${ENV_NAME} - Jenkins Pipeline ${ACTION} Summary</p>
				<p>Jenkins url: <a href='${env.BUILD_URL}/>link</a></p>
				<p>Pipeline Blueocean： <a href='${env.JENKINS_URL}blue/organizations/jenkins/${env.JOB_NAME}/detail/${env.JOB_NAME}/${env.BUILD_NUMBER}/pipeline'>${env.JOB_NAME}(pipeline page)</a></p>
			${env.JENKINS_URL}blue/organizations/jenkins/${env.JOB_NAME}/detail/${env.JOB_NAME}/${env.BUILD_NUMBER}/pipeline
				<ul>
				<li> Branch built: '${env.BRANCH_NAME}' </li>
				<li> ACTION: $ACTION</li>
				<li> REGION: ${AWS_REGION}</li>
				</ul>
				""",
				recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']],
				to: "${EMAIL}",
				subject: "[${ENV_NAME}] - ${env.JOB_NAME}-${env.BUILD_NUMBER} [$AWS_REGION][$ACTION]",
				attachLog: true
				)
        }
    }*/
}
