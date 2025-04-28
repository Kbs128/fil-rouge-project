pipeline {
    agent any

    environment {
        // Définit la clé d'authentification pour SonarQube
        SONARQUBE_URL = 'http://35.88.247.54:9000'
        SONARQUBE_TOKEN = 'sqp_77e99502d57384ba59cc634801e9e7eada83ec42'
    }

    tools {
        // Utilise l'installation SonarQube configurée dans Jenkins
        sonar 'SonarScanner' // Remplacer par le nom exact de ton scanner SonarQube
    }

    stages {
        stage('Checkout') {
            steps {
                // Récupère le code depuis GitHub
                checkout scm
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Exécute le scanner SonarQube
                    sh """
                    sonar-scanner \
                        -Dsonar.projectKey=fil-rouge-project \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=${env.SONARQUBE_URL} \
                        -Dsonar.login=${env.SONARQUBE_TOKEN}
                    """
                }
            }
        }
    }

    post {
        always {
            // Ce bloc est exécuté après les étapes du pipeline
            echo 'Pipeline terminé.'
        }

        success {
            echo 'Analyse SonarQube réussie!'
        }

        failure {
            echo 'L\'analyse SonarQube a échoué.'
        }
    }
}
