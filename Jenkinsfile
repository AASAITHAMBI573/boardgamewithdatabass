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

        stage('Commit & Push Manifest') {
            steps {
                script {
                    // Checkout code from git
                    git branch: 'master', credentialsId: 'git-credential', url: 'https://github.com/AASAITHAMBI573/boardgamewithdatabass.git'
                    sh "git config user.email 'aasai05071993@gmail.com'"
                    sh "git config user.name 'Aasai'"
                    sh("sed -i 's+boardgame: .*+boardgame:v${BUILD_NUMBER}+g' deploymentsfiles/deployment.yaml")
                    
                    withCredentials([gitUsernamePassword(credentialsId: 'git-credential', gitToolName: 'Default')]) {
                        sh 'git fetch origin'
                        sh 'git add -u'
                        sh 'Update deployment image to version v${BUILD_NUMBER}'
                        sh 'git push origin master'
                    }
                }
            }
        }
    }
}
