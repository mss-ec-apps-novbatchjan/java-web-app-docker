node {
     def buildNumber = Build_Number
    stage ("checkout code"){
        git credentialsId: 'gitHub_credentials', url: 'https://github.com/mss-ec-apps-novbatchjan/java-web-app-docker.git'
    }
    stage ("build"){
        def mavenHome = tool name: "maven3.8.5", type: "maven"
        sh "${mavenHome}/bin/mvn clean package"
    }
    stage ("Build docker image"){
        sh "docker build -t dockernovbatch/java-web-app-docker:${buildNumber} ."
    }
    stage("docker login and push"){
        withCredentials([string(credentialsId: 'dockerHub_credentials', variable: 'dockerHub_credentials')]) {
       sh "docker login -u dockernovbatch -p ${dockerHub_credentials}"
}
       sh "docker push dockernovbatch/java-web-app-docker:${buildNumber}"
    }
    stage("deploy application as a container to deplyoment server"){
        sshagent(['Docker-dev-server-ssh']) {
        sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.2.170 docker rm -rf javawebappcontainer || true"
        sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.2.170 docker run -d -p 8080:8080 --name javawebappcontainer dockernovbatch/java-web-app-docker:${buildNumber} "
}
    }
}
