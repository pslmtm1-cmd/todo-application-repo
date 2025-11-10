pipeline {
  agent any

  environment {
    DOCKER_IMAGE = "pslmtm1/todo-application"
    DOCKER_CREDENTIALS = "docker-hub-credentials"
    DOCKER_CREDENTIAL_ID = 'docker-hub-credentials'
    JAR_FILE = "taget/*.jar"
  }

  stages {
    stage('Checkout Source Code') {
      steps {
        git branch: 'master', url: 'https://github.com/pslmtm1-cmd/todo-application-repo.git'
      }
    }
    stage('Build with Maven') {
      steps {
        sh "mvn clean package -DskipTests"
      }
    }

    stage('Build and Push Docker Image') {
      steps {
        script {
          sh "docker build -t ${DOCKER_IMAGE}:latest ."
          withCredentials([usernamePassword(credentialsId: DOCKER_CREDETIAL_ID, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
            sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
            sh "docker push ${DOCKERHUB_USERNAME}/${IMAGE_TAG}"
            sh 'docker logout'
          }
        }
      } 
    }

    stage('Deploy with Docker compose') {
      steps {
        script {
          sh "docker compose up -d"
          sleep 10
          sh "docker network inspect todo-network"
          sh "docker volume inspect mysql-data"
          sh "docker ps | grep todo-application-1"
          sh "docker ps | grep mysql-db-1"
          sh "curl -s http://localhost:8082 | grep 'ToDo Application'"
        }
      } 
    }
    stage('Teardown Docker Compose') {
      steps {
        sh "docker compose down -v"
      }
    }

    stage('Clean Workspace') {
      steps {
        sh "rm -rf *"
      } 
    }
  } 
    post {
      success {
        echo "Pipeline finished successfully!"
      }
      failure {
        echo "Pipeline Failed!"
      }
    } 
  } 
