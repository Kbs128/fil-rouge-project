pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'SonarQube'   // Nom de ton serveur Sonar dans Jenkins
        SONARQUBE_TOKEN = credentials('sonar-token')  // ID de ton credential
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE_SERVER}") {
                    script {
                        def scannerHome = tool 'SonarScanner' // <- Ici on charge SonarScanner installé dans Jenkins
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                              -Dsonar.projectKey=fil-rouge-project \
                              -Dsonar.sources=. \
                              -Dsonar.host.url=http://35.88.247.54:9000 \
                              -Dsonar.login=${SONARQUBE_TOKEN}
                        """
                    }
                }
            }
        }
    }
}
