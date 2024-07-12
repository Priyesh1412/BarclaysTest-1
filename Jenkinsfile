pipeline {
	    agent any
	

	        // Environment Variables
	        environment {
	        MAJOR = '1'
	        MINOR = '0'
	        //Orchestrator Services
	        UIPATH_ORCH_URL = "https://arun-ha-orchestrator.westus2.cloudapp.azure.com/" 
	        UIPATH_ORCH_TENANT_NAME = "Default"
			UIPATH_ORCH_TENANT_NAME_PROD = "prod"
	        UIPATH_ORCH_UAT_FOLDER_NAME = "UAT"
	    }
	

	    stages {
	
	        // Printing Basic Information
	        stage('Preparing'){
	            steps {
	                echo "Jenkins Home ${env.JENKINS_HOME}"
	                echo "Jenkins URL ${env.JENKINS_URL}"
	                echo "Jenkins JOB Number ${env.BUILD_NUMBER}"
	                echo "Jenkins JOB Name ${env.JOB_NAME}"
	                echo "GitHub BranhName ${env.BRANCH_NAME}"
	                checkout scm
	

	            }
	        }
	

	         // Build Stages
	        stage('Build') {
	            steps {
	                echo "Building..with ${WORKSPACE}"
					checkout([$class: 'GitSCM',  
			        branches: [[name: '*/main']],  
					doGenerateSubmoduleConfigurations: false, 
					extensions: [], 
					submoduleCfg: [], 
					userRemoteConfigs: [[credentialsId: '08faa6b7-374c-4acf-b36b-eac49805f3e2', url: 'https://gitlab.com/hrdepartment/jenkins/jenkinsproject.git']]]) 
	                UiPathPack (
	                      outputPath: "${WORKSPACE}\\Output\\${env.BUILD_NUMBER}", 
	                      projectJsonPath: "${WORKSPACE}",
	                      version:[$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
						  traceLevel: 'None'
	        )
	            
		UiPathAssets (
            assetsAction: DeployAssets(), 
            credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: '1ac97ae8-f44f-48ab-9ede-4f8b6c5990b2'],
            filePath: "${WORKSPACE}\\Assets.csv", 
            folderName: "${UIPATH_ORCH_UAT_FOLDER_NAME}",
            orchestratorAddress: "${UIPATH_ORCH_URL}", 
	        orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
            traceLevel: 'None'
        )
	        }}
	         // Test Stages
	        stage('Staging Environment') {
	            steps {
	                echo 'Testing..the workflow...'
					mail bcc: 'arunnasa0290@gmail.com', body: " Hi, \n \n Please follow the link below to approve deployment into Staging. \n \n http://10.0.0.8:8080/job/jenuipath/ \n \n Regards, \n Arun" , cc: 'arunnasa0290@gmail.com', from: 'jenkins@uipath.com', replyTo: 'arunnasa0290@gmail.com', subject: 'Your Approval Required', to: env.APPROVERS 

					timeout(time: 14, unit: 'DAYS') { 

					input message: 'Please approve the deployment to staging', submitter:"Arun" 
	            }
	        
	

	         // Deploy Stages
	        //stage('Deploy to UAT') {
	            //steps {
	                echo "Deploying ${BRANCH_NAME} to UAT "
	                UiPathDeploy (
	                packagePath: "${WORKSPACE}\\Output\\${env.BUILD_NUMBER}", 
	                orchestratorAddress: "${UIPATH_ORCH_URL}", 
	                orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
	                folderName: "${UIPATH_ORCH_UAT_FOLDER_NAME}",
	                environments: 'INT',
	                //credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: 'APIUserKey']
	                credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: '1ac97ae8-f44f-48ab-9ede-4f8b6c5990b2'],
					traceLevel: 'None',
					entryPointPaths: 'Main.xaml'
	

	        )
	            }
	        }


	        stage('Production Environment') { 

          steps { 

              mail bcc: 'arunnasa0290@gmail.com', body: " Hi, \n \n Please follow the link below to approve deployment into production. \n \n http://10.1.2.5:8080/job/jenuipath/ \n \n Regards, \n Arun", cc: 'arunnasa0290@gmail.com', from: 'jenkins@uipath.com', replyTo: 'arunnasa0290@gmail.com', subject: 'Your Approval Required', to: env.APPROVERS 

              timeout(time: 14, unit: 'DAYS') { 

                  input message: 'Please approve the deployment to Production', submitter:"Arun" 

              } 

          } 

      } 


//post { 

 //     success { 

   //       echo "Process  with version ${MAJOR}.${MINOR}.${env.BUILD_NUMBER} was successfully deployed into Production." 

     // } 

 //}
  
//}
}}