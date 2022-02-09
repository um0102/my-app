node{
   stage('SCM Checkout'){
     git 'https://github.com/um0102/my-app.git'
   }
   stage('Compile-Package'){
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
   stage('Build Docker Imager'){
   sh 'docker build -t umeshindedocker/mydockerimg .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u umeshindedocker -p ${dockerPassword}"
    }
   sh 'docker push umeshindedocker/mydockerimg'
   } 
    stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 18.134.210.163:8083"
   sh "docker tag umeshindedocker/mydockerimg 18.134.210.163:8083/nexusimg"
   sh 'docker push 18.134.210.163:8083/nexusimg'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
    }
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest umeshindedocker/mydockerimg' 
   }
  }
}
