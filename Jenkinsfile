pipeline {
    agent any
    
    environment {
        SONAR_TOKEN = credentials('sonar-token') // Utilise un token d'accès SonarQube stocké dans les credentials de Jenkins
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                script {
                    // Utilisation de SonarQube Scanner pour analyser le projet
                    withSonarQubeEnv('SonarQube') {
                        sh '''
                        sonar-scanner \
                            -Dsonar.projectKey=fil-rouge-project \
                            -Dsonar.sources=. \
                            -Dsonar.host.url=http://sonarqube:9000 \
                            -Dsonar.login=$SONAR_TOKEN
                        '''
                    }
                }
            }
        }
        
        stage('Post Actions') {
            steps {
                script {
                    // Attendre la fin de l'analyse de SonarQube avant de passer à l'étape suivante
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
