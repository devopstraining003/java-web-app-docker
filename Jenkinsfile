node{
    def buildNumber = BUILD_NUMBER
    stage('GIT CHECKOUT'){
        git url: 'https://github.com/devopstraining003/java-web-app-docker.git',branch:'master'
    }
    
    stage('MVN BUILD'){
        def mavenHome =  tool name: "Maven", type: "maven"
        def mavenCMD = "${mavenHome}/bin/mvn"
        sh "${mavenCMD} clean package"
    }
    
    stage('DOCKER BUILD'){
        sh "docker build -t devopstraining003/java-web-app:${buildNumber} ."
    }
    
    stage('DOCKER LOGIN'){
        
       withCredentials([string(credentialsId: 'docker_passwd', variable: 'docker_passwd')]) {
          sh "docker login -u devopstraining003 -p ${docker_passwd}"   
       }
    }
    
    stage('DOCKER PUSH'){
        sh "docker push devopstraining003/java-web-app:${buildNumber}"
    }
    
    stage('REMOVE DOCKER IMAGE'){
        sh "docker rmi devopstraining003/java-web-app:${buildNumber}"
    }
    
    stage("Deploy to Server"){
        sshagent(['Deploy_SSH']) {
            sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.28.33 docker rm -f java-web-app || true"
            sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.28.33 docker run  -d -p 8080:8080 --name java-web-app devopstraining003/java-web-app:${buildNumber}"
        }    
    }
}
