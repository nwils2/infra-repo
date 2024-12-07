def COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE': 'danger',
    ]
    
def currentDate = new Date()
def formattedDate
def dateFormat = new java.text.SimpleDateFormat("yyyy-MM-dd HH:mm:ss")
dateFormat.setTimeZone(TimeZone.getTimeZone("America/Chicago")) // To be updated

pipeline {
    agent any
    
    environment {
        AWS_ACCESS_KEY_ID     = credentials('jenkins-aws-secret-key-id') // To be updated
        AWS_SECRET_ACCESS_KEY = credentials('jenkins-aws-secret-access-key') // To be updated
    }
    
    
    tools {
        terraform 'Terraform' 
    } 

    stages {
        
        // Welcome Stage
        stage('Stage 1: Greeting') {
            steps {
                echo '################################################'
                echo 'WELCOME TO eCommerce infrastructure Deployment'
                echo '################################################'
				script {
                    // Setting time
					formattedDate = dateFormat.format(currentDate)
				}
            }
        }
        
        // Git Checkout to clone the repository
        stage('Stage 2: Git checkout') {
            steps {
                echo '1. Check out from git'
                git branch: 'main', credentialsId: 'github_login', url: 'https://github.com/jddbetambo/airbnb.git' // To be updated
            }
        }
        
        // Start building the insfrastructure
        // Terraform init
        stage('Stage 4: Terraform Initialization') {
            steps {
                echo 'Terraform init code ....'
                sh 'terraform init'
            }
        }
        
        // Terraform validate
        stage('Stage 5: Terraform Code Validation') {
            steps {
                echo 'Terraform validate code ....'
                sh 'terraform validate'
            }
        }
        
        // Terraform plan
        stage('Stage 6: Terraform Code to plan the infrastructure') {
            steps {
                echo 'Terraform planning the insfrastructure ....'
                sh 'terraform plan'
            }
        }
		
		
		// Terraform code scanning
		stage('Checkov scan') {
            steps {
               
                sh '''
                checkov -d . --skip-check CKV_AWS_79,CKV2_AWS_41
                '''
            }
        }
        
        // Terraform apply
        stage('Stage 7: Terraform Code to appply/destroy the plan') {
            steps {
                echo 'Terraform apply the plan ....'
                sh 'terraform ${action} --auto-approve'
            }
        }
    }
    
	 post { 
		// To be updated
		always { 
			slackSend channel: '#jdd-jenkins-cicd', 
			color: COLOR_MAP[currentBuild.currentResult], 
			message: "********************* \n *Infrastructure Deployment* \n ********************* \n *Date*: ${formattedDate} \n *Initiated by*: Engineer JDD. \n *Server*: ${env.BUILD_URL} \n *Workspace*:${WORKSPACE} \n *Job Name*: ${env.JOB_NAME} \n *Build Number*: ${env.BUILD_NUMBER} \n *Result*: $currentBuild.currentResult", 
			teamDomain: 'jddtechinc', 
			tokenCredentialId: 'slack-login'
			
			mail from: 'jddmangan@gmail.com',  
            subject: 'Jenkins Build Result', 
            to: 'awsjdd2@gmail.com',
            body: "********************* \n *Infrastructure Deployment* \n ********************* \n *Date*: ${formattedDate} \n *Initiated by*: Engineer JDD. \n *Server*: ${env.BUILD_URL} \n *Workspace*:${WORKSPACE} \n *Job Name*: ${env.JOB_NAME} \n *Build Number*: ${env.BUILD_NUMBER} \n *Result*: $currentBuild.currentResult" 
		}
	}

}