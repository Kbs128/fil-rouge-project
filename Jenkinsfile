pipeline {
    agent any

    environment {
        DOCKER_HUB_REPO = 'babs32/Fil-rouge-project'
        IMAGE_TAG = "latest"
    }

    triggers {
        githubPush() // Déclenche sur push GitHub
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/Kbs128/fil-rouge-project.git'
            }
        }

        stage('Build Application') {
            steps {
                sh './mvnw clean install' // ou `npm install && npm run build` selon ton projet
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_HUB_REPO}:${IMAGE_TAG}")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-creds', url: '']) {
                    dockerImage.push()
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh 'docker stop mon-app || true'
                sh 'docker rm mon-app || true'
                sh "docker run -d --name mon-app -p 8080:8080 ${DOCKER_HUB_REPO}:${IMAGE_TAG}"
            }
        }
    }
}
