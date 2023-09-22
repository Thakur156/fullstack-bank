pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    
    environment {
        SCANNER_HOME = tool 'sonar-server'
    }
    
    stages {
        stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./app/backend --disableYarnAudit --disableNodeAudit', odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('SONARQUBE ANALYSIS') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh "$SCANNER_HOME/bin/sonar-server -Dsonar.projectName=Bank -Dsonar.projectKey=Bank"
                }
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        
        stage('Backend') {
            steps {
                dir('/root/.jenkins/workspace/Bank/app/backend') {
                    sh "npm install"
                }
            }
        }
        
        stage('Frontend') {
            steps {
                dir('/root/.jenkins/workspace/Bank/app/frontend') {
                    sh "npm install"
                }
            }
        }
        
        stage('Deploy to Container') {
            steps {
                sh "npm run compose:up -d"
            }
        }
    }
}
