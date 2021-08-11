pipeline {

  agent any

  environment {
    DOCKER_IMAGE = "lengochung1/dockerwebapp"
    BUILD_NUMBER = "${env.BUILD_NUMBER}"
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
        sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
        sh "docker image ls | grep ${DOCKER_IMAGE}"
        withCredentials([usernamePassword(credentialsId: 'dockerHub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
            sh 'echo $DOCKER_PASSWORD | docker login --username $DOCKER_USERNAME --password-stdin'
            sh "docker push ${DOCKER_IMAGE}:latest"
            sh "docker push ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
        }

        //clean to save disk
        sh "docker image rm ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
        sh "docker image rm ${DOCKER_IMAGE}:${DOCKER_TAG}"
      }
    }
        stage('Cloning Git') {
                steps {
                git 'https://github.com/hunglna8/argocd.git'
            }
        }
        stage('Search and replace') {
            steps {
                script{
                    sh "ls -l"
                }
            }
        }
        stage('Update file yml to gitops'){
            steps{
                script{
                   
                    withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                    def encodedPassword = URLEncoder.encode("$GIT_PASSWORD",'UTF-8')
                    //sh "git config user.email lengochung.ptit@gmail.com"
                    //sh "git config user.name hungba"
                    sh "git remote rm origin"
                    sh "git remote add origin https://github.com/hunglna8/argocd.git"
                    sh "sed -i 's/latest/${env.BUILD_NUMBER}/g' ${WORKSPACE}/helmchart/templates/deployment.yaml"
                    sh "git branch"
                    sh "git branch -r"
                    sh "git status"
                    sh "git log --oneline"
                    sh "git add --all"
                    sh "git commit -m OK"
                    sh "git push -f https://${GIT_USERNAME}:${encodedPassword}@github.com/${GIT_USERNAME}/argocd.git"
                    }
                
                }//steps
            }//stage

        }//stages
    
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