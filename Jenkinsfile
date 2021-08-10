pipeline {

  agent none

  environment {
    DOCKER_IMAGE = "lengochung1/dockerwebapp"
  }

  stages {


    stage("build") {
      agent { node {label 'master'}}
      environment {
        DOCKER_TAG="${GIT_BRANCH.tokenize('/').pop()}-${GIT_COMMIT.substring(0,7)}"
      }
      steps {
        sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} . "
        sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"
        sh "docker image ls | grep ${DOCKER_IMAGE}"
        withCredentials([usernamePassword(credentialsId: 'dockerHub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
            sh 'echo $DOCKER_PASSWORD | docker login --username $DOCKER_USERNAME --password-stdin'
            sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
            sh "docker push ${DOCKER_IMAGE}:latest"
        }

        //clean to save disk
        sh "docker image rm ${DOCKER_IMAGE}:${DOCKER_TAG}"
        sh "docker image rm ${DOCKER_IMAGE}:latest"
      }
    }
    stage('Update GIT') {
      steps {
        script {
           
            withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                def encodedPassword = URLEncoder.encode("$GIT_PASSWORD",'UTF-8')
                sh "git config user.email lengochung.ptit@gmail.com"
                sh "git config user.name hungba"
                sh "git add ."
                sh "git commit -m 'Triggered Build: ${env.BUILD_NUMBER}'"
                sh "git push https://${GIT_USERNAME}:${encodedPassword}@github.com/${GIT_USERNAME}/dockerwebapp.git"
            }
          
        }
      }
}
  }

  post {
    success {
      echo "SUCCESSFUL"
    }
    failure {
      echo "FAILED"
    }
  }
}