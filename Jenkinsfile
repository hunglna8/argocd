node {

    checkout scm

    stage('Build and Push Docker Image...') {
        steps {
            script {
                // CUSTOM REGISTRY
                docker.withRegistry('https://registry.hub.docker.com', 'dockerHub') {
                      
                        /* Build the container image */            
                    def dockerImage = docker.build("lengochung1/dockerwebapp:${env.BUILD_ID}")
                      
                        /* Push the container to the custom Registry */
                    dockerImage.push()
                      
                }
                    /* Remove docker image*/
                sh 'docker rmi -f lengochung1/dockerwebapp:${env.BUILD_ID}'   
            }
        } 
    }
    
}