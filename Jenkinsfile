pipeline {
    agent any


    stages {
        stage('Git Checkout') {
            steps {
                git credentialsId: 'git-pass', url: 'https://github.com/AASAITHAMBI573/boardgamewithdatabass.git'
            }
        }
    }
}
