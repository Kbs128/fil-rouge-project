pipeline {
    agent any

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

        stage('Install Python and Dependencies') {
            steps {
                script {
                    // Installer Python et pip dans l'image Docker
                    sh '''
                        apt-get update && apt-get install -y python3 python3-pip
                        python3 --version
                        pip3 --version
                    '''
                    // Installer les dépendances Python avec pip
                    sh 'pip3 install -r requirements.txt'
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
