# CI/CD with Jenkins, Ansible, Docker, Kubernetes on AWS
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

# 2.CI/CD pipeline using Git, Jenkins and Maven

## Setup Jenkins Server
- Setup a Linux EC2 instance
- Install Java
- Install Jenkins
- Start Jenkins
- Access Web UI on port 8080
1. First we need to launch an EC2 instance with Keypair
```
Name: Jenkins_Server
AMI: Amazon Linux 2
Instance type: t2.micro
key pair login : generate a key pair and store it locally to securely connect to your instance
Security group name: Jenkins_Security_Group
Inbound rules: 
type: ssh, protocol: TCP, Port range: 22
type: Custom TCP, Protocol: TCP, Port Range: 8080
Configure storage: 1x 8 GiB gp2 root volume
```
2. Connect to that instance using ssh. 
- In terminal change the directory of folder to the directory where your key_pair.pem file is stored
- Change the file permissions of key_pair.pem file
```
chmod 0400 key_pair.pem
```
- ssh into EC2 instance using
```
ssh -i key_pair.pem ec2-user@public_ipv4_address
```
- Become root user
```
sudo su -
```
- Run these commands to install java and jenkins
```
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key

amazon-linux-extras install epel
amazon-linux-extras install java-openjdk11
yum install jenkins
```
- Default jenkins server is inactive we need to start it 
```
service jenkins start
```
- To check whether jenkins service has started, we use
```
service jenkins status
```
- Now we can access the jenkins from web UI on port 8080 using
```
public_ipv4_address:8080
```
- To get administrator password of Jenkins use this command and paste that password in Jenkins UI
```
cat /var/lib/jenkins/secrets/initialAdminPassword
```
- For now skip to install any plugin and click 'Start using jenkins' 
![image](https://user-images.githubusercontent.com/56789226/218373747-384f1198-368f-4642-b878-546c6401b16a.png)

##  Run 1st Jenkins Job
- Go to `Dashboard` -> `New item`
```
JobName: HelloWorldJob
Type: Free Style Project
Build Step: Execute shell
echo "Hello World!"
uptime
```
- Apply and click Save. Now click on "Build Now" and Jenkins will build our job. To check output click on green tick mark in build history section.
![image](https://user-images.githubusercontent.com/56789226/218374373-a7e698c1-1957-4892-ae6d-bbfbdfca9702.png)

##  Integrate Git with Jenkins
- Install Git on Jenkins Instance
- Install GitHub Plugin on Jenkins GUI
- Configure Git on Jenkins GUI

1.Install git on jenkins server
```
yum install git
```
2.Install github plugin on jenkins GUI

Click on `Manage Jenkins` -> `Manage Plugin` -> `Available Plugin` -> Search GitHub -> Choose it and click Install without Restart

3.Configure git on jenkins GUI

`Manage Jenkins` -> `Global Tool Configuration` -> Setup git
```
name: Git
Path to Git executable: git
```
## Run Jenkins job to pull code from GitHub
- Create New Item
```
Job Name: PullCodeFromGithub
FreeStyleProject
Description: Pull Code From GitHub
Source Code Management: Git
Repository URl: URL
If private repository then provide credentials
```
- Build the job and check Console Output. Clone is successful. 
![image](https://user-images.githubusercontent.com/56789226/218378437-25b8abf2-ce1a-4348-a550-97b60e0bff81.png)

## Integrate Maven with jenkins
- Setup Maven on jenkins server
- Setup Environment variables (JAVA_HOME, M2, M2_HOME)
- Install Maven Plugin on jenkins GUI
- Configure Maven and Java on jenkins gui
<hr>

- Setup Maven on jenkins server

```
sudo su -
cd /opt
wget https://dlcdn.apache.org/maven/maven-3/3.9.0/binaries/apache-maven-3.9.0-bin.tar.gz
tar -xvzf apache-maven-3.9.0-bin.tar.gz
mv apache-maven-3.9.0 maven
```

- Setup environment variables

```
cd ~
vi .bash_profile
 M2_HOME=/opt/maven
 M2=/opt/maven/bin
 JAVA_HOME=/usr/lib/jvm/java-11-openjdk-11.0.16.0.8-1.amzn2.0.1.x86_64
 PATH=$PATH:$HOME/bin:$JAVA_HOME:$M2_HOME:$M2
source .bash_profile
echo $PATH
```

To apply the changes we have made to .bash_profile, either we can logout and log back in or run `source .bash_profile` command. It will upload the changes.

- Install Maven plugin on Jenkins GUI

`Manage Jenkins` -> `Manage_Plugins` -> `Available Plugin` -> Search Maven and install `Maven Integration` plugin and select install without restart.

- Configure Maven and Java on Jenkins GUI

`Manage Jenkins` -> `Global Tool Configuration`

```
JDK
Name: java-11
JAVA_HOME: /usr/lib/jvm/java-11-openjdk-11.0.16.0.8-1.amzn2.0.1.x86_64
Maven
Name: maven-3.9
MAVEN_HOME: /opt/maven
```
## Build a Java project using Jenkins
```
Item name: FirstMavenproject
Type: Maven Project
Description: First Maven Project
Git
Repository URL: https://github.com/yash-s-patil/hello-world.git
Root POM: pom.xml
Goals and options: clean install
```

Build this job and it will generate an artifact called webapp.war and stored in
` /var/lib/jenkins/workspace/FirstMavenProject/webapp/target/webapp.war `

![image](https://user-images.githubusercontent.com/56789226/218389613-e226c5c1-d1b7-4503-9698-fa4e8f1141b6.png)































