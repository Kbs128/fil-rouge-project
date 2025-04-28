pipeline {
    agent any

    tools {
        sonarScanner 'SonarScanner'  // Nom que tu as configuré dans Jenkins > Global Tool Configuration
    }

    environment {
        SONARQUBE_SERVER = 'SonarQube'   // Nom de ton serveur Sonar dans Jenkins
        SONARQUBE_TOKEN = credentials('sonar-token')  // ID du credential Jenkins où tu as stocké ton token Sonar
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SonarQubeScanner}") {
                    sh '''
                        sonar-scanner \
                          -Dsonar.projectKey=fil-rouge-project \
                          -Dsonar.sources=. \
                          -Dsonar.host.url=http://35.88.247.54:9000 \
                          -Dsonar.login=${SONARQUBE_TOKEN}
                    '''
                }
            }
        }
    }
}
