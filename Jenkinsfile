
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
              script {
                // Run Trivy scan and generate JSON report
                sh "trivy fs --format json --output trivy-report.json ."
                archiveArtifacts artifacts: 'trivy-report.json', fingerprint: true

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
      stage ('docker build') {
        steps {
          script {
            sh "docker build -t arjundocker92/hello-world:${BUILD_NUMBER} ."
          }
        }
      }
      stage ('Scann Image with Trivy') {
        steps {
          script {
             trivy image --format json --output trivy-image-report.json arjundocker92/hello-world:${BUILD_NUMBER}"
             archiveArtifacts artifacts: 'trivy-image-report.json', fingerprint: true
          }
        }
      }
      stage ('Pushing Image into docker hub') {
        steps {
          script {
              withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                    sh "docker push arjundocker92/hello-world:${BUILD_NUMBER}"
                }
          }
        }
      }
      stage ('deploy') {
        steps {
          script {
              sh "docker stop application || true"
              sh "docker rm application || true"
              sh "docker run -itd --name application -p 9000:8080 arjundocker92/hello-world:${BUILD_NUMBER}"
          }
        }
      }
    }
}
            
