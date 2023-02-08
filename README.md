# CI-CD-with-Jenkins-Ansible-Docker-Kubernetes-on-AWS
# 1.Introduction
## Tools
- Git - Local version control system
- GitHub - Distributed version control system
- Jenkins - Continuous integration tool.
- Maven - Build tool
- Ansible - Configuration management and deployment tool
- Docker - Containerisation 
- Kubernetes - Container management tool
- Setup all these environments on AWS

## Devops Workflow
- Source code is with the developer.During the development phase developers commit there code to github.
- We need to build the code. For building the code we use continuous integration tool called Jenkins. Once jenkins build the code it generates artifacts. Those artifacts we need to deploy on the target environment.
- We will use ansible as a deployment tool. Ansible can deploy our application on target environments like VM, docker or kubernetes.
- We will deploy the application first on VM, then on Docker and last on Kubernetes.

![1](https://user-images.githubusercontent.com/56789226/217443148-60aa77bc-3a6c-45f1-a457-ba178b19a8dc.png)

## What do we cover
1. Build and deploy on tomcat server

Setup CI/CD with github, jenkins, Maven and tomcat 
- Setup Jenkins
- Setup and configure maven and git
- Setup Tomcat Server
- Integrate github, maven, tomcat server with jenkins
- Create a CI and CD job
- Test the deployment   

![image](https://user-images.githubusercontent.com/56789226/217444103-cf0a4e10-b41a-4b03-9cb2-c063c8b99d21.png)

2. Deploy artifacts on a container

Setup CI/CD with github, jenkins, maven and docker
- Setting up docker environment 
- Write docker file
- Create an image and container on docker host
- Integrate docker host with jenkins
- Create CI/CD job on jenkins to build and deploy on a container.

![image](https://user-images.githubusercontent.com/56789226/217444732-44021d22-bb06-4812-91b6-faf4b7ec8bad.png)

3. Deploy artifacts on a container with the help of ansible

CI/CD with github, jenkins, maven, ansible and docker
- Setup Ansible server
- Integrate Docker host with ansible
- Ansible playbook to create image
- Ansible playbook to create container
- Integrate Ansible with Jenkins
- CI/CD job to build code on ansible and deploy it on a docker container.

![image](https://user-images.githubusercontent.com/56789226/217445584-eee9c660-24cf-4974-b127-c313735a636d.png)

4. Deploy artifacts on Kubernetes

CI/CD with github, jenkins, maven, ansible and kubernetes
- Setup Kubernetes (EKS)
- Write pod, service and deployment manifest files
- Integrate kubernetes with ansible
- Ansible playbooks to create deployment and service
- CI/CD job to build code on ansible and deploy it on kubernetes.

![image](https://user-images.githubusercontent.com/56789226/217446225-1203bcb5-394a-44cc-80c3-6e01b88a7e49.png)

## What is CI and CD
- Source Code is in the local workstation. From there we will commit it to the version control system (git). Then the continuous integration tool will pull our code automatically and build the code and run unit test. This is called as **Continuous integration**
- During this continuous integration process we build and compile our source code and generate artifacts. Then we need to deploy these artifacts on the target environment. (staging, test, dev, Q/A, pre-prod). Once it is deployed on staging environment we do the regression test and performance test. Once it passes the test it is deployed on production environment. When we do all these things without manual intervention it is called as **Continuous deployment**
- When we need manual approvals to deploy it into the production environment, we call it as **continuous delivery**

## Resources to setup Devops CI/CD pipeline.
- AWS free tier account
- GitHub account
- MobaXterm- For Windows user.For MAC and Linux user- Terminal will work.
- Git Installed.

## Source Code 
- Fork this repositoy - [Source Code](https://github.com/yankils/hello-world)




