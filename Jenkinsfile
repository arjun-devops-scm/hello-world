
pipeline {
  agent any 
   tools {
     maven 'maven'
   }
    stages {
      stage ('test') {
        steps {
          script {
            println("######################### STARTED MAVEN TEST ##############################")
            withCredentials([file(credentialsId: 'SETTINGS_XML_FILE_ID', variable: 'SECRET_FILE')]) {
              sh "mvn test -s ${SECRET_FILE}"
            }
          }
        }
      }
    }
}
            
