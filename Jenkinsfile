pipeline {
  environment {
    registry = "leandrothiery/jenkins-docker-test"
    DOCKER_PWD = credentials('docker-login-pwd')
  }
  agent {
    docker {
      image 'thingswise/node-docker'
      args "--publish list 3000:3000 -w /app -v /var/run/docker.sock:/var/run/docker.sock"
      args '--entrypoint=\'\''
    }
  }
  options {
    skipStagesAfterUnstable()
  }
  stages {
    stage("Build"){
      steps {
        sh 'echo hello there'
        sh 'npm install'
      }
    }
    stage("Test"){
      steps {
        sh 'npm test'
      }
    }
    stage("Build & Push Docker image") {
      steps {
        sh 'docker image build -t $registry:$BUILD_NUMBER .'
        sh 'docker login -u leandrothiery -p $DOCKER_PWD'
        sh 'docker image push $registry:$BUILD_NUMBER'
        sh 'docker image rm $registry:$BUILD_NUMBER'
      }
    }
    stage('Deploy and smoke test') {
      steps{
        sh './jenkins/scripts/deploy.sh'
      }
    }
    stage('Cleanup') {
      steps{
        sh './jenkins/scripts/cleanup.sh'
      }
    }
  }
}