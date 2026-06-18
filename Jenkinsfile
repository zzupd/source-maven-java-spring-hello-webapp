pipeline {
  agent none
  stages {
    stage('Checkout') {
      agent {
        docker { image 'maven:3-eclipse-temurin-12' }
      }
      steps {
        git branch: 'main', 
        url: 'https://github.com/zzupd/source-maven-java-spring-hello-webapp.git'
      }
    }
    stage('Test Application') {
      agent {
        docker { image 'maven:3-eclilpse-temurin-21' }
      }
      steps {
        sh 'mvn test'
      }
    }
    stage('Build Application') {
      agent {
        docker { image 'maven:3-eclipse-temurin-21' }
      }
      steps {
        sh 'mvn clean package -DskipTests=true'
      }
    }
    stage('Build Container Image') {
      agent { label 'controller' }
      steps {
        sh 'docker image build -t myhello:v1 .'
      }
    }
    stage('Tag Container Image') {
      agent { label 'controller' }
      steps {
        sh 'docker image tag myhello:v1 kjj/myhello:v$BUILD_NUMBER'
        sh 'docker image tag myhello:v1 kjj/myhello:latest'

      }
    }
    stage('Push Container Image') {
      agent { label 'controller' }
      steps {
        withDockerRegistry(credentialsId: 'docker-registry-credential', url: 'https://index.docker.io/v1/') {
          sh 'docker image push kjj/myhello:v$BUILD_NUMBER'
          sh 'docker image push kjj/myhello:latest'
        }
      }
    }
    stage('Run Container') {
      agent { label 'controller' }
      steps {
        sh 'docker container run --detach --name myhello -p 80:8080 kjj/myhello:latest'
      }
    }
  }
}

