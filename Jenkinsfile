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
        stage('unit test'){
            steps{
                dir('/var/lib/jenkins/workspace/3-tier/app/backend/')
                    sh 'npm test'
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
                            $SCANNER_HOME/bin/sonar_scanner -Dsonar.projectKey=3-tier-proj -Dsonar.projectName=3-tier-proj
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
    }
}