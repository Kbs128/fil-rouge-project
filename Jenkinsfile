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
                    // Properly format the workspace path for Docker volume mounting
                    def workspace = pwd()
                    def dockerWorkspace = workspace.replace('\\', '/').replace('C:', '/c')
                    
                    bat """
                        docker run --rm ^
                        -v "${dockerWorkspace}:/app" ^
                        -w /app ^
                        python:3.8-slim ^
                        pip install -r requirements.txt
                    """
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    bat "docker build -t ${DOCKER_HUB_REPO}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                    bat """
                        docker login -u %DOCKER_USER% -p %DOCKER_PASSWORD%
                        docker push ${DOCKER_HUB_REPO}:${IMAGE_TAG}
                    """
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    bat """
                        docker stop mon-app 2>NUL || EXIT /B 0
                        docker rm mon-app 2>NUL || EXIT /B 0
                        docker run -d --name mon-app -p 8080:8080 %DOCKER_HUB_REPO%:%IMAGE_TAG%
                    """
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
