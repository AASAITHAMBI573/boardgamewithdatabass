pipeline{
    agent any

    tools{
        maven 'maven3'
    }

    environment {
        SCANNER_HOME=tool 'sonar-scanner'
        GIT_REPO_NAME = 'boardgamewithdatabass'
        GIT_USER_NAME = 'AASAITHAMBI573'
    }

    stages{

        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }
        
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
                sh "docker build -t boardgame ."
                sh "docker tag boardgame aasaithambi5/boardgame:v${BUILD_NUMBER}"
            }
        }

        stage("Docker Image Push Registry"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker-credential', toolName: 'docker'){ 
                       sh "docker push aasaithambi5/boardgame:v${BUILD_NUMBER}"
                    }
                }
            }
        }

        stage('Docker Images Delete'){
            steps{
                sh "docker rmi aasaithambi5/boardgame:v${BUILD_NUMBER}"
                sh "docker rmi boardgame:latest"
            }
        }

        stage("Checkout from SCM") {
               steps {
                   git branch: 'master', credentialsId: 'git-credential', url: 'https://github.com/AASAITHAMBI573/boardgamewithdatabass.git'
               }
        }
    }
}
