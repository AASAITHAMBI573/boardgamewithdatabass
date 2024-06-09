pipeline{
    agent any

    tools{
        maven 'maven3'
    }

    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }

    stages{
        stage('Git Checkout Code'){
        steps{
            git credentialsId: 'git-credential', url: 'https://github.com/AASAITHAMBI573/boardgamewithdatabass.git'
            }
        }

        stage('Maven Compile'){
            steps{
                sh "mvn compile"
            }
        }

        stage('Maven Unit Tests'){
            steps{
                sh "mvn test"
            }
        }

        stage('SonarQube Code Analysis'){
            steps{
                withSonarQubeEnv('sonar-server') {
                sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=boardgame -Dsonar.projectName=boardgame"
                }
            }
        }

        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }

        stage('Docker Build & Tag'){
            steps{
                        sh 'docker build -t aasaithambi5/boardgame:v${BUILD_NUMBER} .'
                        sh 'docker tag boardgame aasaithambi5/boardgame:v${BUILD_NUMBER}'
                        sh 'docker tag boardgame aasaithambi5/boardgame:latest'
            }
        }

        stage("Docker Image Push Registry"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker-credential', toolName: 'docker'){ 
                       sh 'docker push aasaithambi5/boardgame:v${BUILD_NUMBER}'
                       sh 'docker tag boardgame aasaithambi5/boardgame:latest'
                    }
                }
            }
        }
    }
}