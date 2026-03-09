pipeline {
  agent any
  tools {
    maven 'Maven3'
    jdk 'JDK17'
  }
  environment {
    DOCKER_HUB = 'robertobreuer/notificaciones'
    IMAGE_TAG = "${BUILD_NUMBER}"
    K8S_NS = 'devops-lab'
  }
  stages {
    stage('Maven Build') {
      agent {
        docker { image 'maven:3.9.6-jdk-17' }
      }
      steps {
        sh 'mvn clean package -DskipTests'
        stash includes: 'target/*.jar', name: 'app-jar'
      }
    }
    stage('Docker Build') {
      steps {
        unstash 'app-jar'
        sh """
          docker build -t ${DOCKER_HUB}:${IMAGE_TAG} .
          docker tag ${DOCKER_HUB}:${IMAGE_TAG} ${DOCKER_HUB}:latest
        """
      }
    }
    stage('Push DockerHub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          sh """
            echo \$PASS | docker login -u \$USER --password-stdin
            docker push ${DOCKER_HUB}:${IMAGE_TAG}
            docker push ${DOCKER_HUB}:latest
          """
        }
      }
    }
    stage('Deploy k8s') {
      steps {
        withKubeConfig([credentialsId: 'minikube-kubeconfig']) {
          sh """
            kubectl set image deployment/notificaciones app=${DOCKER_HUB}:latest -n ${K8S_NS}
            kubectl rollout status deployment/notificaciones -n ${K8S_NS}
          """
        }
      }
    }
  }
}
