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

        stage('Update Deployment File'){
            environment{
                GIT_REPO_NAME = "boardgamewithdatabass"
                GIT_USER_NAME = "AASAITHAMBI573"
            }
            steps {
                git branch: 'master', credentialsId: 'git-credential', url: 'https://github.com/AASAITHAMBI573/boardgamewithdatabass.git'
            }
        }

        stage('Commit & Push Manifest'){
            steps{
                withCredentials([string(credentialsId: 'git-credential', variable: 'git-credential')]){
                sh '''
                    git config user.email "aasai05071993@gmail.com" 
                    git config user.name "Aasai"
                    sed -i "s/boardgame: .*/boardgame:v${BUILD_NUMBER}/g" deploymentsfiles/deployment.yaml
                    git add .
                    git commit -m "Update deployment image to version v${BUILD_NUMBER}"
                    git push https://${gitopspasswd}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                    
                '''
                }
            }
        }
    }
}
