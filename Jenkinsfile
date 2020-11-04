currentBuild.displayName = "Final_Demo # "+currentBuild.number

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
		
		
		stage('login'){
			steps{
			 	script{
					withCredentials([sshUserPrivateKey(credentialsId: 'master', keyFileVariable: 'master', passphraseVariable: '', usernameVariable: 'ubuntu')]) {
    sh ''' ssh  ubuntu@3.227.239.56
				     
				     '''
}
				    
				}
			}
		}
		stage('ansible playbook'){
			steps{
			 	script{
				    sh '''final_tag=$(echo $Docker_tag | tr -d ' ')
				     echo ${final_tag}test
				     sed -i "s/docker_tag/$final_tag/g"  deployment.yaml
				     '''
				    ansiblePlaybook become: true, installation: 'ansible', inventory: 'hosts', playbook: 'ansible.yaml'
			      //ansiblePlaybook become: true, installation: 'ansible', inventory: 'hosts', playbook: 'ansible.yaml'
				//ansiblePlaybook become: true, installation: 'ansible', inventory: 'hosts', playbook: 'ansible.yaml', sudo: true	
				}
			}
		}
		      }
}
              
		
             	     	         
