pipeline {
    agent any
    
    tools {
        git 'Default'
    }

    stages {
        stage('Start') {
            steps {
                git branch: 'develop', changelog: false, poll: false, url: 'https://github.com/maxGaurav123/app_gaurav.makhija.git'
            }
        }
    }
}