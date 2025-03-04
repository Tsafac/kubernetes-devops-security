pipeline {
    agent any

    environment {
        // Récupère le token stocké dans Jenkins
        SONAR_TOKEN = credentials('sonar-token')  // Remplace 'sonar-token' par l'ID du credential créé dans Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('SonarQube Analysis') {
            steps {
                script {
                    def mvn = tool 'Maven' // Assure-toi que tu as bien configuré l'outil Maven dans Jenkins
                    withSonarQubeEnv('SonarQube') {
                        // Passe le token dans la commande Maven
                        sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.login=${SONAR_TOKEN}"
                    }
                }
            }
        }
    }
}

