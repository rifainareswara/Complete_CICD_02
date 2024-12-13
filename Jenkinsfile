pipeline {
	agent {
        	label 'agent-dso'
    	}
	tools {
		nodejs 'NodeJS'
	}
	environment {
		SONAR_PROJECT_KEY = 'cicdsonar'
		SONAR_SCANNER_HOME = tool 'SonarQubeScanner'	
    		SONAR_PROJECT_NAME = 'cicdsonar'
		SONARQUBE_CREDENTIALS = credentials('jenkins-analysis')
		JOB_NAME_NOW = 'cicd02'
		ECR_REPO = 'iquantawsrepo'
		IMAGE_TAG = 'latest'
		ECR_REGISTRY = '358966077154.dkr.ecr.us-east-1.amazonaws.com'
		ECS_CLUSTER = 'iquant-ecs'
		ECS_SERVICE = 'iquant-ecs-svc'
		ALB_TARGET_GROUP_ARN = 'ecs-iquant-svc-tg'
	}
	stages {
		stage('Checkout') {
            		steps {
                		checkout scmGit(
                    		branches: [[name: '*/main']], 
                    		extensions: [], 
                    		userRemoteConfigs: [[
                        	credentialsId: 'cicdsonar',
                        	url: 'git@github.com:rifainareswara/Complete_CICD_02.git'
                    		]]
                		)
           		}
        	}
		stage('Unit Test'){
			steps {
				sh 'npm test'
				sh 'npm install'		
			}
		}
		stage('SAST SonarQube') {
            agent {
              docker {
                  image 'sonarsource/sonar-scanner-cli:latest'
                  args '--network host -v ".:/usr/src" --entrypoint='
              }
            }
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    sh 'sonar-scanner -Dsonar.projectKey=cicdsonar -Dsonar.qualitygate.wait=true -Dsonar.sources=. -Dsonar.host.url=https://sonar.jobseeker.software -Dsonar.token=$SONARQUBE_CREDENTIALS' 
                }
            }
        }
// 		stage('SonarQube Analysis') {
//     steps {
//         withSonarQubeEnv(installationName: 'SonarQube', credentialsId: 'jenkins-analysis') {
//             sh '''
//                 ${SONAR_SCANNER_HOME}/bin/sonar-scanner \
//                 -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
//                 -Dsonar.projectName=${SONAR_PROJECT_NAME} \
// 		-Dsonar.token=sqp_dc71bb0a5041b6b0d18ad864141a16a473cecb84 \
//                 -Dsonar.sources=. \
//                 -Dsonar.host.url=https://sonar.jobseeker.software \
//                 -Dsonar.scm.disabled=true \
//                 -Dsonar.javascript.node.maxspace=8192
//             '''
//         }
//     }
// }
		// stage('Docker Image'){
		// 	steps {
		// 		script {
		// 			docker.build("${ECR_REGISTRY}/${ECR_REPO}:${IMAGE_TAG}")
		// 		}
		// 	}
		// }
		// stage('Trivy Scan'){
		// 	steps {
		// 		sh 'trivy --severity HIGH,CRITICAL --no-progress --format table -o trivy-report.html image ${JOB_NAME_NOW}:latest'
		// 	}
		// }
		// stage('Login to ECR'){
		// 	steps {
		// 		sh """
		// 		aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 358966077154.dkr.ecr.us-east-1.amazonaws.com
		// 		"""
		// 	}
		// }
		// stage('Push Image to ECR'){
		// 	steps {
		// 		script {
		// 		docker.image("${ECR_REGISTRY}/${ECR_REPO}:${IMAGE_TAG}").push()
		// 		}
		// 	}
		// }
	}
}
