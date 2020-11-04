def getDockerTag() {
 def tag = sh script: 'git rev-parse HEAD', returnStdout: true 
 return tag
}
pipeline{
     agent any
	environment {
          Docker_tag = getDockerTag()
	}
	
        stages{

              stage('Quality Gate Status Check'){
                  steps{
                      script{
			      withSonarQubeEnv('sonarserver') { 
			      sh "java -version"
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
                sh 'docker login -u vinutha25 -p pa**word123'
                sh 'docker push vinutha25/new:$Docker_tag'
				      
			      }
		
			      }
			      
		
                }
		stage('Deployment to Kubernetes'){
			steps {
				script {
kubernetesDeploy configs: 'deployment.yaml', enableConfigSubstitution: true,  kubeconfigId: 'k8s1'

					
					
				}
				
			}
			      }
		      }
}
              
		
             	     	         
