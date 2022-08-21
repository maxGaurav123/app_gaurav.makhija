pipeline {
    agent any

    environment {
        scannerHome = tool name: 'sonar_scanner_dotnet'
        sonarProjectKey = 'sonar-gauravmakhija'
        username = 'admin'
        appName = 'SampleApp'
        kubernetesCluster = 'kubernetes-demo'
        kubernetesProjectId = 'gothic-dreamer-358719'
        kubernetesNameSpace = 'kubernetes-cluster-gauravmakhija'
    }

    options {
	  buildDiscarder logRotator(daysToKeepStr: '15', numToKeepStr: '10')
	  disableConcurrentBuilds()
	  timeout(time: 1, unit: 'HOURS')
	  timestamps()
	}

    tools {
        git 'Default'
    }

    stages {
        stage('Start') {
            steps {
                echo 'Pulling...' + env.BRANCH_NAME
                git branch: "${env.BRANCH_NAME}", changelog: false, poll: false, url: 'https://github.com/maxGaurav123/app_gaurav.makhija.git'
            }
        }
        stage('Nuget restore') {
            steps {
                bat 'dotnet restore'
            }
        }
        stage('Start sonarqube analysis') {
            when {
				branch 'master'
			}
            steps {
                echo "Starting Sonarqube Analysis"
                withSonarQubeEnv('Test_Sonar') {
                    bat "${scannerHome}\\SonarScanner.MSBuild.exe begin /k:\"${sonarProjectKey}\" /d:sonar.verbose=true"
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
                bat 'dotnet test -l:trx;LogFileName=TestExecutionResult.xml'
            }
        }
         stage("Stop Sonarqube Analysis") {
            when {
				branch 'master'
			}
            steps {
                echo "Stopping Sonarqube Analysis"
                withSonarQubeEnv('SonarQube') {
                    bat "${scannerHome}\\SonarScanner.MSBuild.exe end"
                }
            }
        }
        stage("Release artufact") {
            when {
				branch 'develop'
			}
            steps {
                echo "Release artifact step"
                bat "dotnet publish -c Release -o ${appName}/app/${userName}"
            }
        }
        stage("Kubernetes deployment") {
             steps {
                bat "gcloud config set project ${kubernetesProjectId}"
                bat "gcloud container clusters get-credentials ${kubernetesCluster} --zone us-central1-c --project ${kubernetesProjectId}"

                bat "powershell -Command \"(gc secrets.yaml) -replace 'namespace-k8s', '${kubernetesNameSpace}' | Out-File -encoding ASCII secrets.yaml\""
                bat "powershell -Command \"(gc configMap.yaml) -replace 'namespace-k8s', '${kubernetesNameSpace}' | Out-File -encoding ASCII configMap.yaml\""
                bat "powershell -Command \"(gc deploymentAndService.yaml) -replace 'namespace-k8s', '${kubernetesNameSpace}' | Out-File -encoding ASCII deploymentAndService.yaml\""

                bat "powershell -Command \"(gc deploymentAndService.yaml) -replace 'imageOfBranch', '${env.BRANCH_NAME}' | Out-File -encoding ASCII deploymentAndService.yaml\""

                bat "kubectl apply -f secrets.yaml"
                bat "kubectl apply -f configMap.yaml"
                bat "kubectl apply -f deploymentAndService.yaml"
            }
        }
    }
}