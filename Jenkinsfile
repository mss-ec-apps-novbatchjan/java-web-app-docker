node {
    def buildNumber = Build_Number
    stage ("Checkout Code"){
        git credentialsId: 'GIT CREDENTIALS', url: 'https://github.com/mss-ec-apps-novbatchjan/java-web-app-docker.git'
    }
    stage ("Build"){
        def mavenHome = tool name: "maven3.8.5", type: "maven"
        sh "${mavenHome}/bin/mvn clean package"
    }
    stage ("Build Docker Image"){
        sh "docker build -t dockernovbatch/java-web-app-docker:${buildNumber} ."
    }
    stage ("Docker Login and Push"){
       withCredentials([string(credentialsId: 'dockerHub_credentials', variable: 'dockerHub_credentials')]) {
          sh "docker login -u dockernovbatch -p ${dockerHub_credentials}"
}
        sh "docker push dockernovbatch/java-web-app-docker:${buildNumber}"
    }
    stage ("Deploy application as a container to deployment server"){
        sshagent(['Docker-dev-server-ssh']) {
      sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.38.104 docker rm -rf javawebapp-container || true"
      sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.38.104 docker run -d -p 8080:8080 --name javawebapp-container dockernovbatch/java-web-app-docker:${buildNumber}"
}
    }
}
