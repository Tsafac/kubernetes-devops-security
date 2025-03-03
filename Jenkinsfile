pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded late
            }
        } 

      stage('Unit Tests') {
            steps {
              sh "mvn test"
            }
            post {
              always{
                junit 'target/surefire-reports/*.xml'
                jacoco execPattern: 'target/jacoco.exec'
              }
            }
        }
      stage('Docker Build and Push') {
            steps {
               sh 'printenv'
               sh 'docker build -t leberi/numeric-app:""$GIT_COMMIT"" .'
            }
        } 
      stage('Docker Build and Push') {
            steps {
              withKubeConfig([credentialsId: 'config'])
                  sh "sed -i 's#replace#siddharth67/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
                  sh "kubectl apply -f k8s_deployment_service.yaml"
            }
        }     
    }
}
