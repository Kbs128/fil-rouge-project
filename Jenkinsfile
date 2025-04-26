pipeline {
    agent any

    environment {
        DOCKER_HUB_REPO = 'babs32/fil-rouge-project'
        IMAGE_TAG = "latest"
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'Main', url: 'https://github.com/Kbs128/fil-rouge-project.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Récupérer et convertir le chemin du workspace pour Docker sous Windows
                    def workspaceUnix = bat(script: 'echo %WORKSPACE%', returnStdout: true).trim()
                    workspaceUnix = workspaceUnix.replaceAll('\\\\', '/').replaceAll('C:', '/c')

                    sh """
                        docker run --rm \
                        -v ${workspaceUnix}:/app \
                        -w /app \
                        python:3.8-slim \
                        pip install -r requirements.txt
                    """
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_HUB_REPO}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh """
                        docker login -u ${DOCKER_USER} -p ${DOCKER_PASSWORD}
                        docker push ${DOCKER_HUB_REPO}:${IMAGE_TAG}
                    """
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    sh '''
                        docker stop mon-app || true
                        docker rm mon-app || true
                        docker run -d --name mon-app -p 8080:8080 ${DOCKER_HUB_REPO}:${IMAGE_TAG}
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ Déploiement réussi !"
        }
        failure {
            echo "❌ Échec du pipeline. Vérifiez les logs."
        }
    }
}
