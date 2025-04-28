pipeline {
    agent any

    environment {
        SONARQUBE_URL = 'http://35.88.247.54:9000'
        SONARQUBE_TOKEN = credentials('sonarqube-token') // Utiliser les credentials Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Verify Python Installation') {
            steps {
                script {
                    // Vérifier la version de Python installée
                    sh 'python3 --version || echo "Python n\'est pas installé"'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') { // Utilise le nom configuré dans Jenkins
                    script {
                        def scannerHome = tool 'SonarScanner' // Utilise le nom configuré pour SonarQube Scanner
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=fil-rouge-project \
                            -Dsonar.sources=. \
                            -Dsonar.host.url=${SONARQUBE_URL} \
                            -Dsonar.login=${SONARQUBE_TOKEN}
                        """
                    }
                }
            }
        }
    }

    post {
        always {
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
