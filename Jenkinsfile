pipeline {
    agent any

    tools {
        python 'Python3' // Assure-toi que Python3 est installé sur Jenkins
    }

    environment {
        SONARQUBE_SERVER = 'SonarQube' // Nom de ton serveur SonarQube dans Jenkins (Manage Jenkins > Configure)
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                    . venv/bin/activate
                    pytest
                '''
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_TOKEN = credentials('sonarqube-token') // Remplacer par ton ID de token si besoin
            }
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                        . venv/bin/activate
                        sonar-scanner \
                        -Dsonar.projectKey=fil-rouge-project \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=$SONAR_HOST_URL \
                        -Dsonar.login=$SONAR_AUTH_TOKEN
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline terminé avec succès!'
        }
        failure {
            echo 'Le pipeline a échoué!'
        }
    }
}
