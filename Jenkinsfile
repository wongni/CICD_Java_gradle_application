pipeline {
  agent any
  environment {
    VERSION = "${env.BUILD_ID}"
  }
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
    stage('docker build & docker push') {
      steps {
        script {
          withCredentials([string(credentialsId: 'docker_pass', variable: 'docker_password')]) {
            sh '''
              docker build -t 192.168.1.96:8083/springapp:${VERSION} .
              docker login -u admin -p ${docker_password} 192.168.1.96:8083
              docker push 192.168.1.96:8083/springapp:${VERSION}
              docker rmi 192.168.1.96:8083/springapp:${VERSION}
            '''
          }
        }
      }
    }
  }
}
