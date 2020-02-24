pipeline {
  environment {
    registry = "ating3/pipeline-tutorial"
    registryCredential = "dockerhub"
    dockerImage = ''
    HOME = '.'
    npm_config_cache = 'npm-cache'
  }
  agent any
  stages {
    stage('Cloning Git') {
      steps {
        git 'https://github.com/tonyt26/pipeline-tutorial.git'
      }
    }
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Test image') {
        steps {
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                script{
                    dockerImage.inside {
                        sh "npm i"
                        sh "npm test"
                    }
                }
            }
        }
    }
    stage('Deploy Image') {
      steps{
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
            script {
                docker.withRegistry( '' , registryCredential ) {
                    dockerImage.push()
                }
            }
        }
      }
    }
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }
  }
}
