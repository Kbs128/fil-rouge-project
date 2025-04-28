pipeline {
    agent any

    environment {
        SONARQUBE_URL = 'http://35.88.247.54:9000'
        SONARQUBE_TOKEN = credentials('sonarqube-token') // Use Jenkins credentials
    }

    tools {
        // Use the correct tool name as shown in the error message
        maven 'Maven' // If you need Maven
        jdk 'JDK' // If you need JDK
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') { // Use the name configured in Jenkins
                    script {
                        def scannerHome = tool 'SonarScanner' // Use the name configured in Jenkins
                        bat """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=fil-rouge-project \
                            -Dsonar.sources=. \
                            -Dsonar.host.url=${SONARQUBE_URL} \
                            -Dsonar.login=${SONARQUBE_TOKEN}
                        """
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline terminé.'
        }
        success {
            echo 'Analyse SonarQube réussie!'
        }
        failure {
            echo 'L\'analyse SonarQube a échoué.'
        }
    }
}
