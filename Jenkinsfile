


node('docker') {
 
    stage 'Checkout'
        checkout scm
    docker.withRegistry("https://registry.hub.docker.com", "cred-docker-registry"){
    
        stage 'Docker Build Prod Image'
            imageTag = (sh (script: "git rev-parse --short HEAD", returnStdout: true))
            dockerName = "nginx-srv"
            sh "docker build . -t ghostgoose33/nginx-custom.${imageTag}"
    
        stage 'Docker Run'
            sh "docker run -d -p 80:80 --network jenkins-net --name ${dockerName} ghostgoose33/nginx-custom.${imageTag}"
    
        stage 'Push NGINX'
            sh "docker push ghostgoose33/nginx-custom.${imageTag}"
    }
}

node ('docker'){
    stage 'Docker push version 2'
        sh "docker push ghostgoose33/nginx-custom.${imageTag}:v2"
}