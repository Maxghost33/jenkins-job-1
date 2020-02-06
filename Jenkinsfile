node('docker') {
 
    stage 'Checkout'
        checkout scm
    
    stage 'Build Source Nginx (just for example - here can be (dotnet build and other))'
        sh "docker build nginx-source/ --build-arg NGINX_VERSION=1.17.7 --build-arg NJS_VERSION=0.3.6 -t ghostgoose33/nginx-source.dev"

    stage 'Docker Build Prod Image'
        imageTag = (sh (script: "git rev-parse --short HEAD", returnStdout: true))
        dockerName = "nginx-srv"
        sh "docker build . -t ghostgoose33/nginx-custom.${imageTag}"
    
    stage 'Docker Run'
        sh "docker run -d -p 80:80 --network jenkins-net --name ${dockerName} ghostgoose33/nginx-custom.${imageTag}"
    
    stage 'Check NGINX'
        sh "docker push ghostgoose33/nginx-custom.${imageTag}"
}

node ('docker'){
    stage 'Docker push version 2'
        sh "docker push ghostgoose33/nginx-custom.${imageTag}:v2"
}