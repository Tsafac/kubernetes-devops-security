pipeline {
    agent any
    stages {
        stage('Build Artifact') {
            steps {
                sh "mvn clean package -DskipTests=true"
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Unit Tests') {
            steps {
                sh "mvn test"
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                    jacoco execPattern: 'target/jacoco.exec'
                }
            }
        }

        stage('Vulnerability Scan -Docker') {
            steps {
                sh 'mvn dependency-check:check'
             }
             post {
                always{
                    dependencyCheckPublisher pattern: 'target/dependency-check'
                }
             }
        }

        stage('Docker Build and Push') {
            steps {
                sh 'printenv'
                sh 'sudo docker build -t leberi/numeric-app:"$GIT_COMMIT" .'
            }
        }


        stage('Kubernetes Deployment - DEV') {
            steps {
                withKubeConfig([credentialsId: 'config']) {
                    sh "sed -i 's#replace#siddharth67/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
                    sh "sudo kubectl apply -f k8s_deployment_service.yaml"
                }
            }
        }

        stage('SCM') {
            steps {
                checkout scm
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_TOKEN = credentials('sonar-token')  // Récupère le token SonarQube depuis Jenkins
            }
            steps {
                script {
                    def mvnHome = tool name: 'Maven'
                    withSonarQubeEnv('SonarQube') {  // Mets le nom de ton serveur SonarQube configuré dans Jenkins
                        sh "${mvnHome}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.login=${SONAR_TOKEN}"
                    }
                    
                }
            }
        }
    }
}

