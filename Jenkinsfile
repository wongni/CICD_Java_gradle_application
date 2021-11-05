pipeline {
  agent any
  stages {
    stage('sonar quality check') {
      steps {
        script {
          withSonarQubeEnv(credentialsId: 'sonar-token') {
            sh 'chmod +x gradlew'
            sh './gradlew sonarqube'
          }

          timeout(time: 1, unit: 'HOURS') {
            def qg = waitForQualityGate()
            if (qg.status != 'OK') {
              error "Pipeline aboarted due to quality gate failure: ${qg.status}"
            }
          }
        }
      }
    }
  }
}
