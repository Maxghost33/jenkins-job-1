properties([
    parameters([
	stringParam(
            defaultValue: '', 
            description: 'Branch', 
            name: 'branch')
    ])
])


node('docker') {
 
    stage 'Checkout'
        checkout([$class: 'GitSCM', 
            branches: scm.${params.branch}, 
            doGenerateSubmoduleConfigurations: false, 
            extensions: [], 
            submoduleCfg: [], 
            userRemoteConfigs: [[]]
        ])
    
    docker.withRegistry("", "cred-docker-registry"){

        stage 'Docker nginx image'
            sh "docker build nginx-source/ -t ghostgoose33/nginx-source.dev"
        
        stage 'Docker Build Prod Image'
            imageTag = (sh (script: "git rev-parse --short HEAD", returnStdout: true))
            dockerName = "nginx-srv"
            sh "docker build . -t ghostgoose33/nginx-custom.rc"
        
        stage 'Docker Run'
                try{
                    tagold = (sh (script: "docker ps | awk '{ print \$2 }' | grep nginx-custom", returnStdout:true))
                } catch(err){
                    tagold = ""
                }
                echo "TAGOLD = ${tagold}"
                echo "IMAGETAG = ${imageTag}"
                if ("${tagold}" != ""){
                    sh "docker stop ${dockerName}"
                    sh "docker rm ${dockerName}"
                }
                sh "docker run -d -p 80:80 --network jenkins-net --name ${dockerName} ghostgoose33/nginx-custom.rc"
            
    
        //stage 'Push NGINX'
        //    if (env.BRANCH_NAME == 'master') {
        //        sh "docker push ghostgoose33/nginx-custom.rc"
        //    }
        
        //stage 'Deploy chronograf'
        //    if (env.BRANCH_NAME == 'master') {
        //        build(job: 'Maxghost33/jenkins-job-2/master', parameters: [[$class: 'StringParameterValue', name:"imageTag", value: "${imageTag}"]], wait: true)
        //    }
    }
}