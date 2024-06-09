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

        stage("quality gate"){
           steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-credential' 
                }
            }
        }
    }
}
