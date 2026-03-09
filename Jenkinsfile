pipeline {
  agent any
  environment {
    DOCKER_HUB = 'robertobreuer/notificaciones'
    IMAGE_TAG = "${BUILD_NUMBER}"
    K8S_NS = 'devops-lab'
  }
  stages {
    stage('Maven Build') {
      steps {
        sh 'mvn clean package -DskipTests'
      }
    }
    stage('Docker Build') {
      steps {
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
            echo $PASS | docker login -u $USER --password-stdin
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
