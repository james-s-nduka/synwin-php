pipeline {
  agent 'docker-slave-1'

  environment {
    PROJECT = 'synwin-php'
    BUILD_NUMBER = "${env.BUILD_NUMBER}"
    IMAGE_VERSION = "v_${BUILD_NUMBER}"
    GIT_URL = "https://github.com/james-s-nduka/${PROJECT}.git"
    ECS_REGISTRY = "https://176249950847.dkr.ecr.eu-west-2.amazonaws.com"
    ECS_CREDS = "ecr:eu-west-2:synwin_AWS_access"
  }

  parameters {
    booleanParam(name: 'DOCKER_HUB', defaultValue: false, description: 'Deploy to DockerHub?')
    string(name: 'SPECIFIER', defaultValue: "master", description: 'Branch Specifier')
  }

  stages
    stage('Checkout') {
      git branch: "${params.SPECIFIER}", url: "${GIT_URL}"
    }

    stage('Docker Linting') {
      sh 'docker run --rm -i hadolint/hadolint < Dockerfile'
    }

    stage('Build') {
      docker.build("${PROJECT}")
    }

    stage('Deploy') {
      docker.withRegistry("${ECS_REGISTRY}", "${ECR_CREDS}") {
          docker.image("${PROJECT}").push("${BUILD_NUMBER}")
      }

      if(params.DOCKER_HUB) {
          sh 'echo DOCKERHUB'
      }
    }
}
