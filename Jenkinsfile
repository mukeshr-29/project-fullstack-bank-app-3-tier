pipeline{
    agent any
    tools{
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment{
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages{
        stage('git checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/mukeshr-29/project-fullstack-bank-app-3-tier.git'
            }
        }
        stage('install dependency'){
            steps{
                sh 'npm install'
            }
        }
        stage('filescan using trivy'){
            steps{
                sh 'trivy fs --format table -o fs-report.html .'
            }
        }
        stage('static code analysis'){
            steps{
                script{
                    withSonarQubeEnv('sonar-server'){
                        sh '''
                            $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=3-tier-proj -Dsonar.projectName=3-tier-proj
                        '''
                    }
                }
            }
        }
        stage('quality gate analysis'){
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage('docker build and tag'){
            steps{
                sh 'docker build -t mukeshr29/bankapp-backend -f /app/backend/Dockerfile .'
            }            
        }
    }
}