def imageName="pandaacademy/backend"
def dockerRegistry = ""
def registryCredentials = "dockerhub"
def dockerTag = ""
pipeline {
    agent {
        label 'agent'
    }
    environment {
	    scannerHome = tool 'SonarQube'

    }
    

    stages {
        stage("display hello") {
            steps {
                echo 'Hello World'
            }
        }
        stage("download frontend") {
            steps {
            	checkout scm		    
                //git branch: 'main', url: 'https://github.com/michalpartyka/Backend.git'
            }
        }
        stage("display files") {
            steps{
                sh 'pip3 install -r requirements.txt'
                sh 'python3 -m pytest --cov=. --cov-report xml:test-results/coverage.xml --junitxml=test-results/pytest-report.xml'
            }
        }
        stage('Sonarqube analysis') {
            steps {
		echo "SonarQube is down"
                /*withSonarQubeEnv('SonarQube') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }*/
            }
        }
        stage("docker build") {
            steps{
                script {
                  // Prepare basic image for application
                    dockerTag = "RC-${env.BUILD_ID}.${env.GIT_COMMIT}"
                  applicationImage = docker.build("$imageName:$dockerTag",".")
                }

            }
        }
        stage("push image") {
            steps{
                script {
                  docker.withRegistry("$dockerRegistry", "$registryCredentials"){
                  applicationImage.push()
                  applicationImage.push("latest")      
                  }
                }

            }
        }
    }

    post {
        always {
            junit testResults: "test-results/*.xml"
            cleanWs()
        }
    }
}
