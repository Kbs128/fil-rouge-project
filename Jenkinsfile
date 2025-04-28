pipeline {
    agent any

    tools {
        maven 'Maven-3.9.6' // <-- Ce que tu ajoutes
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'Main', url: 'https://github.com/Kbs128/fil-rouge-project.git'
            }
        }
        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh 'mvn clean verify sonar:sonar'
                }
            }
        }
    }
}
