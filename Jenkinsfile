pipeline {
    agent any

    tools {
        // Définir l'outil Python si disponible dans Jenkins (vérifie si l'outil Python3 est configuré)
        python 'Python3' // Assure-toi que Python3 est installé sur Jenkins
    }

    environment {
        // Variables d'environnement si nécessaire
        VIRTUAL_ENV = "${workspace}/venv"
    }

    stages {
        stage('Declarative: Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Créer un environnement virtuel et activer
                    sh '''
                        python3 -m venv venv
                        . venv/bin/activate
                        pip install --upgrade pip --break-system-packages
                        pip install -r requirements.txt --break-system-packages
                    '''
                }
            }
        }

        stage('Run Tests') {
            steps {
                // Exemple de tests avec pytest ou une autre commande
                script {
                    sh '''
                        . venv/bin/activate
                        pytest --maxfail=1 --disable-warnings -q
                    '''
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Analyser le code avec SonarQube
                    sh '''
                        . venv/bin/activate
                        sonar-scanner
                    '''
                }
            }
        }

        stage('Declarative: Post Actions') {
            steps {
                echo '❌ Le pipeline a échoué !'  // Ou un message en fonction du succès
            }
        }
    }

    post {
        always {
            // Nettoyage ou actions à faire après l'exécution du pipeline
            echo 'Pipeline terminé.'
        }

        success {
            echo '✅ Pipeline réussi !'
        }

        failure {
            echo '❌ Le pipeline a échoué.'
        }
    }
}
