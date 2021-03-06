pipeline {
  agent {
    kubernetes {
      defaultContainer 'docker'
      yaml '''
      apiVersion: v1
      kind: Pod
      metadata:
        namespace: jenkins
      spec:
        volumes:
        - name: docker-socket
          hostPath:
            path: /var/run/docker.sock
        containers:
        - image: docker:20-dind
          name: docker
          securityContext:
            privileged: true
          volumeMount:
          - mountPath: /var/run/docker.sock
            name: docker-socket
      '''
    }
  }
  stages {
    stage('Build') {
      steps {
        dir('adoptopenjdk-15-dind') {
          sh 'docker build -t kinerp/maven:3.8.1-adoptopenjdk-15-dind .'
        }
        dir('adoptopenjdk-16-dind') {
          sh 'docker build -t kinerp/maven:3.8.1-adoptopenjdk-16-dind .'
        }
	dir('openjdk-17-dind') {
          sh 'docker build -t kinerp/maven:3.8.3-openjdk-17-dind .'
        }
      }
    }
    stage('Push') {
      steps {
        withDockerRegistry(registry: [credentialsId: 'DOCKER_IMAGE_UPLOAD_TOKEN']) {
          sh 'docker push  kinerp/maven:3.8.1-adoptopenjdk-15-dind'
          sh 'docker push  kinerp/maven:3.8.1-adoptopenjdk-16-dind'
          sh 'docker push  kinerp/maven:3.8.3-openjdk-17-dind'
        }
      }
    }
  }
}
