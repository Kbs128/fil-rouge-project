pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'sonar'  // Le nom du serveur que tu as configuré dans Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'Main', url: 'https://github.com/Kbs128/fil-rouge-project.git'
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv("${SONARQUBE_SERVER}") {
                    sh 'mvn clean verify sonar:sonar'
                }
            }
        }
    }
}
