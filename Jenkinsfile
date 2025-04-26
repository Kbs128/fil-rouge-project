pipeline {
    agent any

    environment {
        DOCKER_HUB_REPO = 'babs32/fil-rouge-project'
        IMAGE_TAG = "latest"
    }

    triggers {
        githubPush()
    }

    options {
        timestamps() // Ajoute des timestamps aux logs
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
                    // Check if requirements.txt exists and install inside a container (for validation only)
                    bat '''
                        IF EXIST backend\\requirements.txt (
                            docker run --rm ^
                            -v "%CD%\\backend:/app" ^
                            -w /app ^
                            python:3.8-slim ^
                            pip install -r requirements.txt
                        ) ELSE (
                            echo "requirements.txt not found in backend directory"
                            exit 1
                        )
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dir('backend') {
                        bat "docker build -t %DOCKER_HUB_REPO%:%IMAGE_TAG% ."
                    }
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                    bat """
                        docker login -u %DOCKER_USER% -p %DOCKER_PASSWORD%
                        docker push %DOCKER_HUB_REPO%:%IMAGE_TAG%
                    """
                }
            }
        }

        stage('Deploy Container') {
            steps {
                bat """
                    docker stop mon-app 2>NUL || EXIT /B 0
                    docker rm mon-app 2>NUL || EXIT /B 0
                    docker run -d --name mon-app -p 8000:8000 %DOCKER_HUB_REPO%:%IMAGE_TAG%
                """
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
        always {
            // Nettoyage prudent
            bat "docker image prune -f"
        }
    }
}
