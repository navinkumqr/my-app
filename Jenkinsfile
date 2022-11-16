node {
    def builNumber = BUILD_NUMBER
    stage('Clone sources') {
        git url: 'https://github.com/navinkumqr/my-app.git'
    }
    stage('Maven clean package'){
        def mavenHome= tool name: "Maven",type: "maven"
         sh "${mavenHome}/bin/mvn clean package"
    }
    stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'Maven', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
    stage('Build docker image') {
        sh "docker build -t navinkumqr/myapp:${builNumber} ."
    }
    stage('Docker login and push image') {
        withCredentials([string(credentialsId: 'DockerHubPwd', variable: 'DockerHubPwd')]) {
            sh "docker login -u navinkumqr -p ${DockerHubPwd}"
        }
        sh "docker push navinkumqr/myapp:${builNumber}"
    }
    stage('Nexus Image Push'){
    sh "docker login -u admin -p admin123 43.205.240.238:8083"
    sh "docker tag navinkumqr/myapp:${builNumber} 43.205.240.238:8083/navin:1.0.0"
    sh 'docker push 43.205.240.238:8083/navin:1.0.0'
    }
    stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
    }
    stage('Docker deployment'){
    sh "docker run -d -p 8090:8080 --name tomcattest navinkumqr/myapp:${builNumber}"
    }
}
