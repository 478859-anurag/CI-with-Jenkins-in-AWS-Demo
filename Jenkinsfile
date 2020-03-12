pipeline {
    agent any 	
	environment {
		
		PROJECT_ID = 'helical-door-261014'
                CLUSTER_NAME = 'k8s-cluster1'
                LOCATION = 'europe-north1-c'
                CREDENTIALS_ID = 'kubernetes'		
	}
	
    stages {	
	   stage('Scm Checkout') {            
		steps {
                  checkout scm
		}	
           }
           
	   stage('Build') { 
                steps {
                  echo "Cleaning and packaging..."
                  sh 'mvn clean package'		
                }
           }
	   stage('Test') { 
		steps {
	          echo "Testing..."
		  sh 'mvn test'
		}
	   }
	   stage('Build Docker Image') { 
		steps {
                   script {
                      myapp = docker.build("478859/super-league:${env.BUILD_ID}")
		      
                   }
                }
	   }
	   stage("Push Docker Image") {
                steps {
                   script {
			   docker.withRegistry('https://registry.hub.docker.com', 'Docker-Hub-Credentials') {
                            myapp.push("${env.BUILD_ID}")		
			   }
		   }
                }
            }
	   
           stage('Deploy to K8s') { 
                steps{
                   echo "Deployment started ..."
		   sh 'ls -ltr'
		   sh 'pwd'
		   sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
                   step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
		   echo "Deployment Finished ..."
            }
          }
    }
}
