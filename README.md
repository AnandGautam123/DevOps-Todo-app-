Building and Deploying a Node.js Application with Docker on Ubuntu




Step 1 - Installation of docker on Ubuntu
The first step is to update the package manager. The second step is to install Docker by using the command "sudo apt-get install docker.io -y" on the terminal. After installation, the Docker version can be checked by typing "sudo docker –version" in the terminal.
Command-
1.	apt-get update
2.	apt-get install docker.io -y
3.	docker --version

 
Step 2 : Clone the code from Github
The code for the project is cloned from GitHub using the command 'git clone <repository_url>'. In this case, the repository URL is
https://github.com/AnandGautam123/DevOps-Todo-app-.git
 

(Here I’m cloning the code from AnandGautam123 account and even you can forks that Repositorie to your github account).

Step 3: Check the files and understand the requirements
Check the files and understand the requirements Once the code has been cloned, we navigate to the directory using the command 'cd react_django_demo1_app' and use the 'ls' command to list the files in the directory. This helps us to understand the requirements of the project.
Commands
- Cd react_django_demo1_app
- ls


In the README.md file, the user can find the requirements for the project. After understanding 'README.md' file the requirements for the project are documented, including the need for Node.js, Java, and npm.  
 

Step 4: Create a docker image using a dockerfile

To Create a Docker image using a Dockerfile A Dockerfile is used to define the configuration of the Docker image. In this step, a Dockerfile is created to build the Docker image for the project.
I suggested to use base image the node image version 19-alpine3.16 from the Docker Hub. 
The Alpine Linux distribution is a lightweight Linux distribution that is commonly used in Docker images because of its small size. The 19-alpine3.16 tag refers to a specific version of the image.


Create a Dockerfile to define the environment for the application. The Dockerfile specifies the base image to use, the packages to install, and any other configuration required for the application.


Step 4: Build the docker image
Build the Docker image Once the Dockerfile has been created, the Docker image is built using the 'docker build' command. The '-t' flag is used to specify the name and tag of the Docker image.
Command
docker build . -t node-todo-app:latest

To check the images, use the command "docker images".



Step 5: Create a docker container
Create a Docker container A Docker container is created using the Docker image that was built in the previous step. The '-d' flag is used to run the container in detached mode, and the '-p' flag is used to map port 8000 of the container to port 8000 of the host machine.

Commands
docker run -d -p8000:8000 node-todo-app:latest

The user can check the running containers by using the command "sudo docker ps". 



Step 6: Open port 8000 in the instance’s security inbound rule
Open port 8000 in the instance’s security inbound rule To allow traffic to access the application, port 8000 needs to be opened in the instance’s security inbound rule.


Step 7: Check if the application is running
Check if the application is running Finally, we can check if the application is running by opening a browser and typing the URL 'http://<host_ip_address>:8000' in a web browser.




**Project on Building and Deploying a Node.js Application with Docker on Ubuntu using Jenkins CI/CD Pipeline
**
 
The project aims to set up a Jenkins cluster consisting of a master node, an agent node, and a live server using three AWS EC2 instances. The project requires installing Java, Jenkins, and Docker on the master and agent nodes. The Jenkins cluster distributes the workload across multiple nodes, and the master node controls and assigns jobs to agent nodes. A pipeline is created to deploy a Node.js application using Docker. The pipeline has stages such as checkout, build, test, push, and deploy, which perform different tasks such as fetching source code from GitHub, building a Docker image, testing the image, pushing it to DockerHub, and deploying it to the live server. The project helps in automating the deployment process and saves time and effort by eliminating manual tasks.

Pre-Requisites:
1.	3 EC2 Instance (1 for Jenkins Master-Node, 1 for Jenkins Agent-Node and 1 for Deploying live sever)
2.	Jenkins and Java Installation in Master-Node
3.	Only Java Installation in Agent-Node
4.	Docker Installation

 

Step 1: Set up a Jenkins Cluster (Master Node and Agent Node) 
•	Jenkins can be configured as a single server or as a cluster of servers, where the workload is distributed across multiple nodes.
•	In a Jenkins cluster, multiple Jenkins instances, called nodes, work together to execute jobs. Each node has its own resources (such as CPU, memory, and disk space) and can perform builds independently.
•	The Jenkins master controls the cluster and assigns jobs to the agent nodes based on their availability and workload.
•	When a job is submitted to the Jenkins master, it decides which node to run the job on, based on the configured load balancing algorithm. The job is then executed on the selected node.
•	Refer this link to setup a agent node to master node
•	Here I have successfully connected agent node to master node and its online. 



Step 2: Create a Node-todo-app-deployment  Job with Pipeline
•	Go to Dashboard and click on New Item 
•	Enter an item name as “node-todo-app-deployment” and select pipeline as a job and click on OK.

 
•	Give job description
•	Select GitHub project and past GitHub project link
•	Here, I used my GitHub project link 

https://github.com/AnandGautam123/DevOps-Todo-app-.git



•	Selected GitHub hook trigger for GITscm polling for Build Triggers. To set-up webhook trigger follow the steps
•	Go to github project link in that go to setting of the project.
•	In setting, click on webhooks and click on add webhook
•	In Payload URL — entre https://<public ip of masternode>:8080/github-webhook/
•	In event you can select as your requirement.
•	Click on Add webhook


 
•	In Pipeline definition, select pipeline script, enter your groovy code.

In a Jenkins pipeline, Groovy code is typically defined in a Jenkinsfile, which is a text file that contains the pipeline definition.The Jenkinsfile is written using the Groovy programming language and is used to define the stages, steps, and other elements of the pipeline.

There are many stages like Checkout, Build, Test, Push and Deploy

1.	Checkout Stage 
In a Jenkins pipeline, the "checkout" stage is used to fetch the source code from a version control system (VCS) such as Git, Subversion, or Mercurial.
In Pipeline, in agent section we have to mention our agent node name like Dev-Agent node.
pipeline {
    	agent { label 'Dev-Agent node' }
    	stages{
        	stage('Checkout'){
            	steps{
                    git url: 'https://github.com/AnandGautam123/DevOps-Todo-app-.git', branch: 'master' 
                	}
                }	
            }
        }
2.	Build Stage 
In a Jenkins pipeline, the "Build" stage is typically the first stage in the pipeline, and it is responsible for building the source code.
The "Build" stage can include any necessary steps to compile the code, package it into a deployable format, and perform any other necessary preparation tasks.
Here in this stage, I am building a docker image using docker file through command and tagging image with AnandGautam123/nodo-todo-app-test:latest'
Note: Install docker and docker compose in Jenkins Master-Node.
        stage('Build’){
            steps{
                sh 'docker build . -t AnandGautam123/nodo-todo-app-test:latest'
            }
        }

3.	Test Stage
In a Jenkins pipeline, the "Test" stage is typically the second stage in the pipeline, and it is responsible for running tests to verify the correctness of the built code.
The "Test" stage can include any necessary steps to execute the tests and generate test reports.
Here in this stage, I am testing docker image by executing command 
docker inspect --type=image AnandGautam123/nodo-todo-app-test:latest
        stage('Test image') {
            steps {
                echo 'testing...'
                sh 'docker inspect --type=image AnandGautam123/nodo-todo-app-test:latest '
            }
        }
4.	Push Stage
In this stage, I am pushing docker image to my Dockerhub public repository and for dockerHub password I have generated separated dockerhub token. 
           stage('Push'){
     steps{
        	     sh "sudo docker login -u AnandGautam123 -p your_personal_Accesss_token"
                   sh 'sudo docker push AnandGautam123/nodo-todo-app-test:latest'
            }
    }  
5.	Deploy Stage
In a Jenkins pipeline, the "Deploy" stage is typically the final stage in the pipeline, and it is responsible for deploying the built and tested artifacts to a target environment.
The "Deploy" stage can include any necessary steps to deploy the built artifacts to a target environment, such as a production server or a container registry.
Here in this stage, I am building docker compose and deploying to another server using SSH command.
        stage('Deploy'){
            steps{
                echo 'deploying on another server'
                sh 'sudo docker stop nodetodoapp || true'
                sh 'sudo docker rm nodetodoapp || true'
                sh 'sudo docker run -d --name nodetodoapp -p 8000:8000 AnandGautam123/nodo-todo-app-test:latest'
                sh '''
                ssh -i Ubuntudemo.pem -o StrictHostKeyChecking=no ubuntu@44.211.144.201 <<EOF
                sudo docker login -u AnandGautam123 -p your_personal_Accesss_token
                sudo docker pull AnandGautam123/nodo-todo-app-test:latest
                sudo docker stop nodetodoapp || true
                sudo docker rm nodetodoapp || true 
                sudo docker run -d --name nodetodoapp -p 8000:8000 AnandGautam123/nodo-todo-app-test:latest
                '''
            }
        }

Final pipeline script
pipeline {
    agent { label 'Dev-Agent node' }
    
    stages{
        stage('Checkout'){
            steps{
                git url: 'https://github.com/AnandGautam123/DevOps-Todo-app-.git', branch: 'master'
            }
        }
        stage('Build'){
            steps{
                sh 'sudo docker build . -t AnandGautam123/nodo-todo-app-test:latest'
            }
        }
        stage('Test image') {
            steps {
                echo 'testing...'
                sh 'sudo docker inspect --type=image AnandGautam123/nodo-todo-app-test:latest '
            }
        }
        
        stage('Push'){
            steps{
        	     sh "sudo docker login -u AnandGautam123 -p your_personal_Accesss_token"
                 sh 'sudo docker push AnandGautam123/nodo-todo-app-test:latest'
            }
        }  
        stage('Deploy'){
            steps{
                echo 'deploying on another server'
                sh 'sudo docker stop nodetodoapp || true'
                sh 'sudo docker rm nodetodoapp || true'
                sh 'sudo docker run -d --name nodetodoapp -p 80:80  AnandGautam123/nodo-todo-app-test:latest'
                sh '''
                ssh -i Ubuntudemo.pem -o StrictHostKeyChecking=no ubuntu@44.211.144.201 <<EOF
                sudo docker login -u AnandGautam123 -p your_personal_Accesss_token
                sudo docker pull AnandGautam123/nodo-todo-app-test:latest
                sudo docker stop nodetodoapp || true
                sudo docker rm nodetodoapp || true 
                sudo docker run -d --name nodetodoapp -p 8000:8000 AnandGautam123/nodo-todo-app-test:latest
                '''
            }
        }
    }
}



 
•	Click on Save and Apply 

•	Finally…! Our Nodo-todo-app-deployment pipeline successfully Checkout, Build, Tested, Pushed and deployed.

•	Docker images successfully pushed to dockerHub repository
 

Step 3: Open port 8000 in the Deploying on live server Instance’s security inbound rule


Open port 8000 in the instance’s security inbound rule To allow traffic to access the application, port 8000 needs to be opened in the instance’s security inbound rule.


Step 4: Check if the application is running
Check if the application is running Finally, we can check if the application is running by opening a browser and typing the URL ‘http://<live server instance public_ip_address>:8000’  (http://44.211.144.201) in a web browser.
 



 







# DevOps-Todo-app-
# DevOps-Todo-app-
