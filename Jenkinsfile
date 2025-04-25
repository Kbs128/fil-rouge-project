pipeline {
    agent {
        docker {
            image 'python:3.8-slim'  // Utiliser l'image Docker officielle de Python
            args '-v /tmp:/tmp' // Optionnel, vous pouvez ajouter des arguments pour volume si nécessaire
        }
    }

    environment {
        DOCKER_HUB_REPO = 'babs32/fil-rouge-project'  // Dépôt Docker Hub
        IMAGE_TAG = "latest"  // Tag de l'image Docker
    }

    triggers {
        githubPush()  // Déclenche sur push GitHub
    }

    stages {
        stage('Clone Repository') {
            steps {
                // Cloner le dépôt GitHub
                git branch: 'Main', url: 'https://github.com/Kbs128/fil-rouge-project.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Installer les dépendances Python avec pip (utilisant python:3.8-slim)
                    sh 'pip install -r requirements.txt'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Construction de l'image Docker avec le tag spécifié
                    dockerImage = docker.build("${DOCKER_HUB_REPO}:${IMAGE_TAG}")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    // Se connecter à Docker Hub avec les credentials stockés dans Jenkins
                    withDockerRegistry([credentialsId: 'docker-hub-creds', url: '']) {
                        // Pousser l'image Docker sur Docker Hub
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    // Arrêter et supprimer le conteneur existant si nécessaire
                    sh 'docker stop mon-app || true'
                    sh 'docker rm mon-app || true'

                    // Lancer le conteneur en arrière-plan et exposer le port 8080
                    sh "docker run -d --name mon-app -p 8080:8080 ${DOCKER_HUB_REPO}:${IMAGE_TAG}"
                }
            }
        }
    }
}
