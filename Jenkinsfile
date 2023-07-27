node{											
   stage('GIT SCM Checkout'){								
     git 'https://github.com/jegan-1995/my-app.git'					
   }
   stage('MAVEN Compile-Package'){
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
   sh 'docker build -t jegan1995/mywebj:0.0.1 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u jegan1995 -p ${dockerPassword}"
   }
   sh 'docker push jegan1995/mywebj:0.0.1'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   }
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest jegan1995/mywebj:0.0.1' 
   }    
   stage('Nexus Image Push'){
   withCredentials([string(credentialsId: 'nexusPass', variable: 'nexusPassword')]) {
   sh "docker login -u admin -p ${nexusPassword} 3.110.158.66:8083"
   sh "docker tag jegan1995/mywebj:0.0.1 3.110.158.66:8083/jegan:1.0.0"
   sh 'docker push 3.110.158.66:8083/jegan:1.0.0'
   }
}
}
