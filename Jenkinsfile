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
        stage('install dependency for backend'){
            steps{
                dir('/var/lib/jenkins/workspace/3-tier/app/backend/'){
                    sh 'npm install'
                }
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
                sh 'docker build -t mukeshr29/bankapp-backend -f /var/lib/jenkins/workspace/3-tier/app/backend/Dockerfile /var/lib/jenkins/workspace/3-tier/app/backend'
                sh 'docker build -t mukeshr29/bankapp-frontend -f /var/lib/jenkins/workspace/3-tier/app/frontend/Dockerfile /var/lib/jenkins/workspace/3-tier/app/frontend'
                sh 'docker tag postgres:15.1 mukeshr29/bankapp-database'
            }            
        }
        stage('trivy img scan'){
            steps{
                sh 'trivy image --format table -o img-report1.html mukeshr29/bankapp-backend'
                sh 'trivy image --format table -o img-report2.html mukeshr29/bankapp-frontend'
                sh 'trivy image --format table -o img-report3.html mukeshr29/bankapp-database'
            }
        }
        stage('docker img push'){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'dockerhub', toolName: 'docker'){
                        sh 'docker push mukeshr29/bankapp-backend'
                        sh 'docker push mukeshr29/bankapp-frontend'
                        sh 'docker push mukeshr29/bankapp-database'
                    }
                }
            }
        }
    }
}