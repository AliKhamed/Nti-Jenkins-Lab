# Jenkins and ArgoCD CI/CD Pipeline

![](https://github.com/AliKhamed/Nti-Jenkins-Lab/blob/main/screenshots/CICD-pipeline-using-Argo-CD-Image-Updater-and-Jenkins.png)

This project demonstrates a CI/CD pipeline using Jenkins to build, push Docker images, and update Kubernetes configurations, and ArgoCD to deploy the application on a Minikube cluster.


## Pipeline Overview

The Jenkins pipeline follows these stages to build, push, edite and deploy Docker images to dockerhub and an Minikube cluster:

1. **Build Docker Image:** Build docker image.

2. **Push Docker Image To DockerHub:** Push docker image to docker hub.

3. **Edit new image in deployment.yaml file:** Edit new image in deployment.yaml file.
   
4. **Deploy with ArgoCD to minikube cluster:** Use ArgoCD to synchronize and deploy the updated configuration from the GitHub repository to the Minikube cluster.

## Prerequisites

    1. Jenkins installed and configured.
    2. Docker installed on Jenkins agents.
    3. Minikube installed and configured.
    4. ArgoCD installed and configured on the Minikube cluster.
    5. DockerHub account and credentials.

## To install and configured ArgoCD on the Minikube cluster:
[repo link](https://github.com/AliKhamed/ivolve_labs/tree/main/oc/lab11)

## Shared Libirary Repository
[repo link](https://github.com/AliKhamed/minikube_shared_library/tree/main)


## Pipeline Stages
```
@Library('Argocd-Shared-Library')_
pipeline {
    agent any
    
    environment {
            dockerHubCredentialsID	            = 'DockerHub'  		    			      // DockerHub credentials ID.
            imageName   		            = 'alikhames/argocd-python-app'     			     // DockerHub repo/image name.
	    k8sCredentialsID	                    = 'kubernetes'	    				     // KubeConfig credentials ID.   
	    gitRepoName 			    = 'Nti-Jenkins-Lab'
            gitUserName 			    = 'Alikhamed'
	    gitUserEmail                            = 'Alikhames566@gmail.com'
	    githubToken                             = 'github-token'
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
                	
			 editNewImage("${githubToken}", "${imageName}", "${gitUserEmail}", "${gitUserName}", "${gitRepoName}")
			
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
```

### Successfully Run Pipeline
![](https://github.com/AliKhamed/Nti-Jenkins-Lab/blob/main/screenshots/jenkins1.png)



### After Run Pipeline Check ArgoCD Application
![](https://github.com/AliKhamed/Nti-Jenkins-Lab/blob/main/screenshots/argocd1.png)


### Check your application
![](https://github.com/AliKhamed/Nti-Jenkins-Lab/blob/main/screenshots/portforward.png)
![](https://github.com/AliKhamed/Nti-Jenkins-Lab/blob/main/screenshots/app2.png)
![](https://github.com/AliKhamed/Nti-Jenkins-Lab/blob/main/screenshots/app1.png)


