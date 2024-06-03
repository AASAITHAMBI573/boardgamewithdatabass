pipeline {
    agent any

        tools {
            jdk 'jdk17'
            maven 'maven3'
        }

        enviornment {
            SCANNER_HOME= tool 'sonar-scanner'
        }

    stages {
        stage('Git Checkout') {
            steps {
                git credentialsId: 'git-pass', url: 'https://github.com/AASAITHAMBI573/boardgamewithdatabass.git'
            }
        }

        stage('Compile') {
            steps {
                sh "mvn compile"
            }
        }

        stage('Test') {
            steps {
                sh "mvn test"
            }
        }

        stage('File System Scan') {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }

        stage('File System Scan') {
            steps {
                withSonarQubeEnv(credentialsId: 'sonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=BoardGame -Dsonar.projectKey=BoardGame \
                    -Dsonar.java.binaries=. '''
                }
            }
        }

        stage('File System Scan') {
            steps {
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-pass'
                }
            }
        }

        stage('Build') {
            steps {
                sh "mvn package"
            }
        }
    }
}


    
