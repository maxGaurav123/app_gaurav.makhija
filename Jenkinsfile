pipeline {
    agent any
    
    environment {
        scannerHome = tool name: 'sonar_scanner_dotnet'
        userName = 'admin'
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
                    bat "${scannerHome}\\SonarScanner.MSBuild.exe begin /k:\"sonar-gauravmakhija\" /d:sonar.verbose=true -d:sonar.cs.xunit.reportpath='Tests/TestResults/sonar-gauravmakhija.xml'"
                }
            }
        }
        stage('Code build') {
            steps {
                bat "dotnet build"
            }
        }
        stage('Test case execution') {
            steps {
                echo 'Testing..'
                bat 'dotnet test -l:trx;LogFileName=NAGPDevOpsTestOutput.xml'
            }
        }
        stage("Stop sonarqube analysis") {
            steps {
                echo "Stopping Sonarqube Analysis"
                withSonarQubeEnv('SonarQube') {
                    bat "${scannerHome}\\SonarScanner.MSBuild.exe end"
                }
            }
        }
        stage("Release artufact") {
            steps {
                echo "Release artifact step"
                bat "dotnet publish -c Release -o ${appName}/app/${userName}"
            }
        }
    }
}