node{
   stage('SCM Checkout'){
     git 'https://github.com/yuvi-011/my-app.git'
   }
  
   stage('maven-buildstage'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
   stage('Build Docker Image'){
   sh 'docker build -t yuvarajeleven/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u yuvarajeleven -p ${dockerPassword}"
    }
   sh 'docker push yuvarajeleven/myweb:0.0.2'
   }
   stage('Nexus Image Push'){
   withCredentials([string(credentialsId: 'nexusPass', variable: 'nexusPassword')]){
   sh "docker login -u admin -p ${nexusPassword} 18.130.172.26:8083"
   }
   sh "docker tag yuvarajeleven/myweb:0.0.2 18.130.172.26:8083/yuvi:1.0.0"
   sh 'docker push 18.130.172.26:8083/yuvi:1.0.0'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest yuvarajeleven/myweb:0.0.2' 
   }
   }

