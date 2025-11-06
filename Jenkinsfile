
pipeline {
  agent {
    label 'jenkins-agent'
  }
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
      stage('Security Scan with Trivy') {
            steps {
                // Run Trivy scan and generate JSON report
                sh "trivy fs --format json --output trivy-report.json ."
                archiveArtifacts artifacts: 'trivy-report.json', fingerprint: true

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
      stage ('Build') {
        steps {
          script {
            withCredentials([file(credentialsId: 'SETTINGS_XML_FILE_ID', variable: 'SECRET_FILE')]) {
             sh "mvn package -s ${SECRET_FILE}"
            }
            }
          }
        }
      stage ('Maven Publish') {
        steps {
          script {
           withCredentials([file(credentialsId: 'SETTINGS_XML_FILE_ID', variable: 'SECRET_FILE')]) {
             sh "mvn deploy  -s ${SECRET_FILE}"
           }
          }
        }
      }
    }
}
            
