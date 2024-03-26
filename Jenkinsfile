node{
   stage('SCM CHECKOUT'){
     git 'https://github.com/elaya1818/CICD.git'
   }
     stage('BUILD-MAVEN'){
      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SONARQUBE ANALYSIS') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
   stage('nexus'){
	    def mvnHome =  tool name: 'maven3', type: 'maven'
	   configFileProvider([configFile(fileId: '380de3c3-d7db-42fd-be43-28a2128ba01d', variable: 'mavensettings')]) {
                  
                  sh "${mvnHome}/bin/mvn -s $mavensettings  deploy -DskipTests=true"
                  
                }
   }
    stage('DOCKER IMAGE BUILD'){
   sh 'docker build -t elavarasan018/myweb:0.0.2 .'
   }
    stage('DOCKERHUB PUSH'){
   withCredentials([string(credentialsId: 'dockerpass', variable: 'dockerPassword')]) {
   sh "docker login -u elavarasan018 -p ${dockerPassword}"
    }
   sh 'docker push elavarasan018/myweb:0.0.2'
   }
   
   stage('NEXUS ARTIFACTORY'){
   sh "docker login -u admin -p admin123 13.200.229.48:8083"
   sh "docker tag elavarasan018/myweb:0.0.2 13.200.229.48:8083/docker-private:1.0.0"
   sh 'docker push 13.200.229.48:8083/docker-private:1.0.0'
   }
  stage('REMOVE PREVIOUS CONTAINER'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('DOCKER DEPLOYMENT'){
   sh 'docker run -d -p 8090:8080 --name tomcattest elavarasan018/myweb:0.0.2' 
   }
}
	stage('email notification'){
	emailext (attachLog: true, body: 'pfa, This is the status of your cicd project pipeline', subject: 'CiCd Pipeline Status', to: 'elavarasan06041999@gmail.com')
	}

	
}	
    
