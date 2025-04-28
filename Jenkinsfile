pipeline {
    agent any

    environment {
        // Define SonarQube URL and authentication token
        SONAR_URL = 'http://35.88.247.54:9000'
        SONAR_TOKEN = 'sqp_77e99502d57384ba59cc634801e9e7eada83ec42'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the project from GitHub
                git 'https://github.com/Kbs128/fil-rouge-project.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Execute SonarScanner to analyze the project
                    sh """
                    sonar-scanner \
                    -Dsonar.projectKey=fil-rouge-project \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=$SONAR_URL \
                    -Dsonar.login=$SONAR_TOKEN
                    """
                }
            }
        }
    }

    post {
        always {
            // Clean up or notify users if needed
        }
    }
}
