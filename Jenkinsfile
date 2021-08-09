node {

    checkout scm

    docker.withRegistry('https://registry.hub.docker.com', 'dockerHub') {

        def customImage = docker.build("lengochung1/dockerwebapp:${env.BUILD_ID}")

        /* Push the container to the custom Registry */
        customImage.push()

    }
    sh 'docker rmi -f lengochung1/dockerwebapp:${env.BUILD_ID}'
    
}