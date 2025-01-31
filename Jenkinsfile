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
        stage('Unit Test') {
            steps {
                sh 'npm test'
                sh 'npm install'        
            }
        }
        stage("SonarQube analysis") {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                        ${SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=test \
                        -Dsonar.projectName="Test Project" \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=https://sonar.dso-jobseeker.com \
                        -Dsonar.token=sqp_baa242570b049e42980a5dfaa1fb101545aa6ee7 \
                        -Dsonar.userHome=/home/ubuntu/.sonar \
                        -Dsonar.sonar.qualitygate.wait=true \
                        -Dsonar.qualitygate.timeout=300 \
                        -Dsonar.verbose=true
                    '''
                }
            }
        }
        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    script {
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