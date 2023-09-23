pipeline {
    agent any
    
    tools{
        jdk 'jdk17'
        nodejs 'node16'
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('git checkout') {
            steps {
               git branch: 'main', url: 'https://github.com/Thakur156/fullstack-bank'
            }
        }
        
        stage('OAWSAP Dependency check') {
            steps {
               dependencyCheck additionalArguments: ' --scan ./ ', odcInstallation: 'DC'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Trivy') {
            steps {
               sh "trivy fs ."
            }
        }
        
        stage('SONARQUBE ANALYSIS') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh " $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Bank -Dsonar.projectKey=Bank "
                }
            }
        }
        
        stage('install dependencies') {
            steps {
               sh "npm install"
            }
        }
        
        stage('Backend') {
            steps {
                sh "cd app/backend && npm install"
            }
        }
        
        stage('frontend') {
            steps {
                sh "cd app/frontend && npm install"
            }
        }
        
        stage('Deploy to Conatiner') {
            steps {
                sh "npm run compose:up -d"
            }
        }
        
    }
}
