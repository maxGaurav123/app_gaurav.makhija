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
                echo "Starting Sonarqube Analysis"
                withSonarQubeEnv('Test_Sonar') {
                    bat "${scannerHome}\\SonarScanner.MSBuild.exe begin /k:\"sonar-gauravmakhija\" /d:sonar.verbose=true"
                }
            }
        }
        stage('Build') {
            steps {
                bat "dotnet build"
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
                bat 'dotnet test -l:trx;LogFileName=NAGPDevOpsTestOutput.xml'
            }
        }
         stage("Stop Sonarqube Analysis") {
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
        stage("Kubernetes deployment") {
             steps {
                bat 'gcloud compute zones list'
                bat 'gcloud config set project gothic-dreamer-358719'
                bat 'gcloud container clusters get-credentials kubernetes-demo --zone us-central1-c --project gothic-dreamer-358719'
                bat 'kubectl apply -f deploymentAndService.yaml'
            }
        }
    }
}