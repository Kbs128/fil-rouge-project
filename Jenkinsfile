pipeline {
    agent any

    environment {
        DOCKER_HUB_REPO = 'babs32/fil-rouge-project'
        IMAGE_TAG = "latest"
    }

    triggers {
        githubPush() // Déclenche sur push GitHub
    }

    stages {
        stage('Clone Repository') {
            steps {
               git branch: 'Main', url: 'https://github.com/Kbs128/fil-rouge-project.git'
           }
       }

        stage('Build Application') {
            steps {
                // Assurez-vous que le fichier mvnw est exécutable
                sh 'chmod +x ./mvnw'  // Ajoute les permissions d'exécution
                sh './mvnw clean install' // Utilise le wrapper Maven
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
                script {
                    withDockerRegistry([credentialsId: 'docker-hub-creds', url: '']) {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    sh 'docker stop mon-app || true'
                    sh 'docker rm mon-app || true'
                    sh "docker run -d --name mon-app -p 8080:8080 ${DOCKER_HUB_REPO}:${IMAGE_TAG}"
                }
            }
        }
    }
}
