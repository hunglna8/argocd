node {

    checkout scm

    docker.withRegistry('https://registry.hub.docker.com', 'dockerHub') {

        def customImage = docker.build("lengochung1/dockerwebapp:v2")

        /* Push the container to the custom Registry */
        customImage.push()
    }
}