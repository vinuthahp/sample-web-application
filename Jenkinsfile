def getDockerTag() {
 def tag = sh script: 'git rev-parse HEAD', returnStdout: true 
 return tag
}
pipeline{

      agent {
                docker {
                image 'maven'
                args '-v $HOME/.m2:/root/.m2'
                }
             }
      environment {
          Docker_tag = getDockerTag()
      }
        
        stages{

              stage('Quality Gate Status Check'){
                  steps{
                      script{
			      withSonarQubeEnv('sonarserver') { 
			      sh "mvn sonar:sonar"
                       	     	}
			      timeout(time: 1, unit: 'HOURS') {
			      def qg = waitForQualityGate()
				      if (qg.status != 'OK') {
					   error "Pipeline aborted due to quality gate failure: ${qg.status}"
				      }
                    		}
		    	    sh "mvn clean install"
		  
                 	}
               	 }  
              }	

              stage('build'){
		      steps {
			      script{
                sh 'docker build . -t vinutha25/new:$Docker_tag'
                withCredentials([usernamePassword(credentialsId: 'ee717238-750c-4f3e-8ac5-fcf90baa881b', passwordVariable: 'dockerps', usernameVariable: 'docker')]) {
    // some block
                    
                sh '''docker login -u vinutha25 -p $docker_password
                docker push vinutha25/new:$Docker_tag
		'''
                }
                
			      }
		      }
              }
		
            }	       	     	         
}
