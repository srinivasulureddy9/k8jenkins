# k8jenkins
k8jenkinspipelinescript




node{
    
    stage("Git Clone"){
    git credentialsId: 'GIT_CREDENTIALS', url: 'https://github.com/srinivasulureddy9/spring-boot-mongo-docker.git'
    }
    stage("Maven Clean Build"){
    def mavenHome = tool name:"Maven-3.6.1", type:"maven"
    def mavenCMD = "${mavenHome}/bin/mvn "
    sh "${mavenCMD} clean package"
    }
    stage("Build Docker Image"){
    sh "docker build -t srinivasulureddy9/spring-boot-mongo ."    
    }
    stage("Docker Push"){
        withCredentials([string(credentialsId: 'DOCKER_HUB_CREDENTIALS', variable: 'DOCKER_HUB_CREDENTIALS')]) {
        sh "docker login -u srinivasulureddy9 -p ${DOCKER_HUB_CREDENTIALS}"
        sh "docker push srinivasulureddy9/spring-boot-mongo "
    }
    stage("Deploy Application in k82 Cluster"){
        kubernetesDeploy(
            configs: 'springBootMongo.yml' ,
            kubeconfigId: 'KUBERNETES_CLUSTER_CONFIG' ,
            enableConfigSubstitution: true
            )
    }    
    }
}
