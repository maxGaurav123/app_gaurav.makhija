pipeline {
    agent any
    
    environment {
        scannerHome = tool name: 'sonar_scanner_dotnet'
        username = 'admin'
        appName = 'SampleApp'
    }

    tools {
        git 'Default'
    }

    stages {
        stage('Start') {
            steps {
                git branch: 'develop', changelog: false, poll: false, url: 'https://github.com/maxGaurav123/app_gaurav.makhija.git'
            }
        }
        stage('Nuget restore') {
            steps {
                bat 'dotnet restore'
            }
        }
        stage('Start sonarqube analysis') {
            steps {
                withSonarQubeEnv('Test_Sonar') {
                    bat "${scannerHome}\\SonarScanner.MSBuild.exe begin /k:\"sonar-gauravmakhija\" /d:sonar.verbose=true"
                }
            }
        }
    }
}