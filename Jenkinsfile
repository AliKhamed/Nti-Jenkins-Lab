@Library('Argocd-Shared-Library')_
pipeline {
    agent any
    
    environment {
        dockerHubCredentialsID	            = 'DockerHub'  		    			      // DockerHub credentials ID.
        imageName   		            = 'alikhames/nti-python-app'     			// DockerHub repo/image name.
	    k8sCredentialsID	            = 'kubernetes'	    				     // KubeConfig credentials ID.    
    }
    
    stages {       
       
        stage('Build Docker image from Dockerfile in GitHub') {
            steps {
                script {
                 	
                 		buildDockerImage("${imageName}")
                      
                }
            }
        }
        stage('Push image to Docker hub') {
            steps {
                script {
                 	
                 		pushDockerImage("${dockerHubCredentialsID}", "${imageName}")
                      
                }
            }
        }

        stage('Edit new image in deployment.yaml file') {
            steps {
                script { 
                	dir('k8s') {
				        editNewImage("${imageName}")
			}
                }
            }
        }
        stage('Deploy on ArgoCD') {
            steps {
                script { 
                	
				deployOnArgoCD("${k8sCredentialsID}")
                    
                }
            }
        }
    }

    post {
        always {
            echo "${JOB_NAME}-${BUILD_NUMBER} pipeline always succeeded"
        }
        success {
            echo "${JOB_NAME}-${BUILD_NUMBER} pipeline succeeded"
        }
        failure {
            echo "${JOB_NAME}-${BUILD_NUMBER} pipeline failed"
        }
    }
}
