
pipeline {
  agent any 
   tools {
     maven 'maven'
   }
    stages {
      stage ('Test') {
        steps {
          script {
            println("######################### STARTED MAVEN TEST ##############################")
            withCredentials([file(credentialsId: 'SETTINGS_XML_FILE_ID', variable: 'SECRET_FILE')]) {
              sh "mvn test -s ${SECRET_FILE}"
            }
          }
        }
      }
      stage ('Sonar Analysis') {
        steps {
          script {
            def SONAR_SCANNER_HOME =   tool name: 'sonar-scanner'
            withSonarQubeEnv('sonar') {
              sh "${SONAR_SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=hello-world"
            }
          }
        }
      }
    }
}
            
