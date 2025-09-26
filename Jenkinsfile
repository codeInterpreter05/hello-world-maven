pipeline {
  agent any
  environment {
    DOCKER_REPO = "sarth2004/maven-hello-world"
    DOCKER_CREDS = "dockerhub-creds"
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build with Maven') {
      steps { sh "mvn -B -DskipTests clean package" }
    }
    stage('Docker Build & Push') {
      steps {
        script {
          def commit = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
          withCredentials([usernamePassword(credentialsId: DOCKER_CREDS, usernameVariable: 'USER', passwordVariable: 'PASS')]) {
            sh """
              docker build -t $DOCKER_REPO:$commit .
              echo $PASS | docker login -u $USER --password-stdin
              docker push $DOCKER_REPO:$commit
              docker tag $DOCKER_REPO:$commit $DOCKER_REPO:latest
              docker push $DOCKER_REPO:latest
            """
          }
        }
      }
    }
  }
}
