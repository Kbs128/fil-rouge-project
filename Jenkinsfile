pipeline {
    agent any
    tools {
        python 'Python-3.10' // <-- adapte le nom selon ton installation Jenkins
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'Main', url: 'https://github.com/Kbs128/fil-rouge-project.git'
            }
        }
        stage('Install dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }
        stage('SonarQube Scan') {
            environment {
                SONAR_SCANNER_HOME = tool 'SonarScanner' // <-- configure SonarScanner dans Jenkins
            }
            steps {
                withSonarQubeEnv('sonar') {
                    sh "${SONAR_SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=fil-rouge-project \
                        -Dsonar.sources=. \
                        -Dsonar.python.version=3.10 \
                        -Dsonar.host.url=$SONAR_HOST_URL \
                        -Dsonar.login=$SONAR_AUTH_TOKEN"
                }
            }
        }
    }
}
