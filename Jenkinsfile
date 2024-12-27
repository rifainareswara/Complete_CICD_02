pipeline {
	agent {
        	label 'agent-dso'
    	}
	tools {
		nodejs 'NodeJS'
	}
	environment {
		SCANNER_HOME = tool 'SonarQubeScanner'
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
        stage("SonarQube analysis"){
            steps{
                withSonarQubeEnv('SonarQube') {
                  sh """
                    sudo ${SCANNER_HOME}/bin/sonar-scanner \
                    -Dsonar.projectKey=test \
                    -Dsonar.sources=."""
                }
            }
        }
        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                script{
                    def qg = waitForQualityGate()
                    if (qg.status != 'OK') {
                        error "Pipeline aborted due to quality gate failure: ${qg.status}"
                    }
                    echo 'Quality Gate Passed' 
                    }   
                }
            }
		}
	}
}
