# Devops Project: Automated CI/CD Pipeline for Web Application using GitHub, Jenkins, Maven, Ansible, Docker, and Kubernetes on AWS
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

# 3.Integrating Tomcat server in CI/CD pipeline
## Setup a tomcat server
- Setup a Linux EC2 instance
- Install java
- Configure Tomcat
- Start Tomcat Server
- Access Web UI on port 8080
<hr>

- First create an EC2 instance for tomcat server
```
Name: Tomcat_Server
AMI: Amazon Linux 2
Instance type: t2.micro
key pair login : use the already generated key pair login during setting up Jenkins_Server
Security group name: Devops_Security_Group
Inbound rules: 
type: ssh, protocol: TCP, Port range: 22
type: Custom TCP, Protocol: TCP, Port Range: 8080
Configure storage: 1x 8 GiB gp2 root volume
```
- ssh into EC2 instance using
```
ssh -i key_pair.pem ec2-user@public_ipv4_address
```
- Become root user
```
sudo su -
```
- Install java
```
amazon-linux-extras install java-openjdk11
```
- Install Tomcat
```
cd /opt
wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.71/bin/apache-tomcat-9.0.71.tar.gz
tar -xvzf apache-tomcat-9.0.71.tar.gz
mv apache-tomcat-9.0.71 tomcat
```
- Now we can start our tomcat server
```
cd tomcat/bin/
./startup.sh
```
- Now we should be able to access the tomcat server from browser using 
```
public_ipv4_address:8080
```
- When we click `Manager App` we get `403 Access Denied` error. To fix the issue we need to edit `context.xml` file.
- Go to tomcat directory and run `find / -name context.xml` to get the location of `context.xml` file.
```
/opt/tomcat/webapps/examples/META-INF/context.xml
/opt/tomcat/webapps/host-manager/META-INF/context.xml
/opt/tomcat/webapps/manager/META-INF/context.xml
```
- We should update `context.xml` file under `host-manager` and `manager` directories. Currently it is only allowing access from localhost, we will comment out the part shown below in the xml. 
```
<!--  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
  allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> -->
```
- Once we updated those files, we need to stop and restart our tomcat server
```
cd tomcat/bin/
./shutdown.sh
./startup.sh
```
- Now we updated the files, we will no longer get `403 Access Denied` error but it will ask username and password in tomcat server.To find the credentials, go under tomcat/conf directory
```
cd tomcat/conf/
vi tomcat-users.xml
```
- We will add below roles and users to the file, and save the file.
```
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<role rolename="manager-jmx"/>
<role rolename="manager-status"/>
<user username="admin" password="admin" roles="manager-gui, manager-script, manager-jmx, manager-status"/>
<user username="deployer" password="deployer" roles="manager-script"/>
<user username="tomcat" password="s3cret" roles="manager-gui"/>
```
- Create link files for tomcat startup.sh and shutdown.sh
```
ln -s /opt/tomcat/bin/startup.sh /usr/local/bin/tomcatup
ln -s /opt/tomcat/bin/shutdown.sh /usr/local/bin/tomcatdown
```
- Once we update the file, we need to stop and restart our tomcat server.
```
tomcatdown
tomcatup
```
![image](https://user-images.githubusercontent.com/56789226/218991188-17a06b23-6f38-4fc8-9962-5988dd536965.png)

## Integrate Tomcat with Jenkins
- Install "Deploy to container"
- Configure tomcat server with credentials 
<hr>

- We can change the password for jenkins server by going to `admin` -> `Configure` and change the password and login again.
- Install `Deploy to container` plugin in Jenkins. Go to `Manage jenkins` -> `Manage Plugins` , find the plugin under available and choose `install without restart`
- Configure Tomcat server with credentials. Go to `Manage Jenkins` -> `Manage Credentials`. We will select `Add credentials`. We will use the credential we have added to `tomcat-user.xml` file for this step. Since these credentials will be used for deploying app, we will add `deployer` credentials which has `manager-script` role.
```
Kind: username with password
username: deployer
pwd: deployer
```
- Now we can create our next job with name `BuildAndDeployJob`. After build step,the artifact will get stored under `webapp/target/` directory as `webapp.war`
```
Kind: Maven Project
SCM: https://github.com/yash-s-patil/hello-world.git
Goal and options: clean install
Post Build Actions: Deploy war/ear to a container
WAR/EAR files: **/*.war
Container: Tomcat 8 (even though we have install v9, this plugin is giving some issues with v9, for that reason we will use v8)
Credentials: tomcat_deployer
Tomcat URL: http://<Public_IP_of_Tomcat_server>:8080/
```
- Save and Build the job. When we go to tomcat server under `Manager App`, you will be able to see our application under `webapp/`
![image](https://user-images.githubusercontent.com/56789226/219000238-f76a19f6-c92e-4189-8df6-30bfd4b64b8c.png)

##  Automate build and deploy using Poll SCM
- Whenever there is a change in the source code and that code is pushed to github, we use to manually build the job and jenkins use to deploy the code on tomcat server. But we don't want to manually execute the job. Whenever there is a change in repository it should automatically identify and execute the build job and deploy the code.
- Go to the existing maven job `BuildAndDeployJob` and click `Configure` and in `Build Triggers` select `Poll SCM`. In the specific period of time it will go and validate whether there is a change in repository or not. If there are no changes build will not happen, if there are changes build will be executed.
```
Schedule: * * * * * 
(go and check every minute if there is any change in repository and execute it) 
```
- Now if we make any changes in the repository, jenkins will automatically identify the changes and build the code using maven and deploy the code on a tomcat server and when you refresh the web page you can see the changes.

# 4.Integrating Docker in CI/CD pipeline
## Setup Docker environment.
- Setup a Linux EC2 instance
- Install docker
- Start docker services
- Basic docker commands
<hr>

- Setup a Linux EC2 instance to make it as a docker host
```
Name: Docker_Host
AMI: Amazon Linux 2
Instance type: t2.micro
key pair login : use the already generated key pair login during setting up Jenkins_Server
Security group name: Devops_Security_Group
```
- ssh into the instance
```
ssh -i key_pair.pem ec2-user@public_ipv4_address
```
- Become root user
```
sudo su -
```
- Install docker
```
yum install docker -y 
```
- Check docker status, whether it is running or not
```
service docker status
```
- To start docker service
```
service docker start
```

## Create a Tomcat container
- To create a docker container we need a docker image. With the help of docker image we can run a command called `docker run` with additional specification and we can create a docker container. We get docker image in two ways:  **Docker Hub** - Docker Hub is a public repository which contains docker images, we can use the command called `docker pull` to pull the image to the local system. Another way is by creating our own Dockerfile . **Dockerfile** contains instructions, what our docker container should get. To create docker image out of Dockerfile we will use `docker build` command. 
- Go to `DockerHub` , search for `Tomcat` official image. We can pick a specific tag of the image, but if we don't specify it will pull the image with latest tag. For this project we will use latest Tomcat image.
```
docker pull tomcat
```
- Next we will create a docker container from this image
```
docker run -d --name tomcat-container -p 8081:8080 tomcat
```
- To be able to reach this container from browser, we need to add port `8081` to our Security Group. We can add a range of port numbers `8081-9000` to Ingress.
- Once we try to reach our container from browser it will give us `404 Not found` error. This is a known issue after tomcat version 9. 
![image](https://user-images.githubusercontent.com/56789226/219844166-da724bb0-10cd-47e1-99cf-780f4e67c06e.png)

##  Fixing Tomcat container issue
- First we need to go inside container by running below command
```
docker exec -it tomcat-container /bin/bash
```
- Once we are inside the container, we need to move the files under `webapps.dist/` to `webapps/`
```
cd webapps.dist
cp -R * ../webapps/
```
- When we refresh the browser page we can see the Apache Tomcat/10.1.5 page.
- However, this solutions is temporary. Whenever we stop our container and restart or create a new container from our tomcat image the same error will be appeared. To overcome this issue, we will create a Dockerfile and create our own Tomcat image. 

## Create a first Docker file (For reference to understand Dockerfile instructions)
- Some of the important docker instructions
```
FROM: to pull the base image
RUN: to execute commands
CMD: to provide defaults for an executing container
ENTRYPOINT: to configure a container that will run as an executable (commands are not overwritten)
WORKDIR: to set the working directory
COPY: to copy a directory from your local machine to the docker container
ADD: to copy files and folders from your local machine to docker containers
EXPOSE: Informs Docker that container listens on the specified network ports at runtime
ENV: to set the environment variables
```
- Example - Install tomcat on Centos 
- Create a Dockerfile
```
vi Dockerfile 
```
- Add these instructions in the Dockerfile and save it
```
FROM centos:latest
RUN yum install java -y
RUN mkdir /opt/tomcat
WORKDIR /opt/tomcat
ADD https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.71/bin/apache-tomcat-9.0.71.tar.gz .
RUN tar -xvzf apache-tomcat-9.0.71.tar.gz
RUN mv apache-tomcat-9.0.71/* /opt/tomcat
EXPOSE 8080
CMD ["/opt/tomcat/bin/catalina.sh", "run"]
```
- To create a image from Dockerfile
```
docker build -t mytomcat .
```
- To create a container from the image 
```
docker run -d --name mytomcat-server -p 8083:8080 mytomcat
```
- We can access the tomcat server from the browser using
```
public_ip_v4_address:8083
```

## Create a customized Dockerfile for Tomcat  

- Create a Dockerfile
```
vi Dockerfile
```
- Enter this instructions in Dockerfile and save it
```
FROM tomcat:latest
RUN cp -R /usr/local/tomcat/webapps.dist/* /usr/local/tomcat/webapps
```
- Create an image from this Dockerfile.
```
docker build -t demotomcat .
```
- Create a docker container from docker image
```
docker run -d --name demotomcat-container -p 8085:8080 demotomcat
```
- We can access the tomcat server from the browser using
```
public_ip_v4_address:8085
```
![image](https://user-images.githubusercontent.com/56789226/219844922-19d189fe-2974-47ef-8ecf-6b2b9fa30adc.png)

## Integrate Docker with Jenkins
- Create a docker admin user
- Install `Public over ssh` plugin
- Add DockerHost to Jenkins `configure systems`
<hr>

- We will create a new user/password called `dockeradmin` and add it to `docker` group
```
useradd dockeradmin
passwd dockeradmin
usermod -aG docker dockeradmin
```
- By default EC2 instance doesn't allow password based authentication we should explicitly enable it
```
vi /etc/ssh/sshd_config
```
- Un-comment the `PasswordAuthentication yes` and comment the `#PasswordAuthentication no` and save it. 
- Restart the service
```
service sshd reload
```
- Now to login as a dockeradmin open new terminal and ssh into EC2 instance as a dockeradmin and enter password
```
ssh -i key_pair.pem dockeradmin@public_ipv4_address
```
- To generate ssh key which we may need in future,enter the following command
```
sudo su - dockeradmin
ssh-keygen
```
- Now login into the jenkins server and go to `Manage Jenkins` -> `Manage Plugins` -> `Available Plugin` and search `Publish over ssh` and install without restart.
- Configure DockerHost over Jenkins. Go to `Manage Jenkins` -> `Configure Systems`. Find `Publish over SSH` -> `SSH Server`. Configure it using below instructions and apply the changes and save
```
Name: dockerhost
Hostname: Private IP of Docker Host(since Jenkins and Docker host are in the same VPC, they would be able to communicate over same network)
Username: dockeradmin
click Advanced
Check use password based authentication
provide password
```
## Jenkins Job to build and copy the artifacts on to docker host
- Create a new Jenkins job, but copy the configurations from `BuildAndDeployJob`
```
Name: BuildAndDeployContainer
Copy from: BuildAndDeployJob
Description: Build code with help of maven and deploy it on docker
SCM: https://github.com/yash-s-patil/hello-world.git
POLL SCM: * * * * *
Build Goals: clean install
Post build actions: Send build artifacts over ssh
SSH server: dockerhost
Source files: webapp/target/*.war
Remove prefix: webapp/target
```
- Save and Apply and click on `Build Now`
- If we go on to the dockerhost and do `ll` we can see `webapp.war` file. Now we need to update the Dockerfile to so that when tomcat container is build webapp.war file is deployed on container and our application should be accessible. 

## Update Tomcat Dockerfile to automate deployment process.
- Now we will create a Docker Image along with artifact(.war file). So that when we launch a new container it will come up with our application
- We can maintain a separate directory called `docker` in root user `/opt` directory to keep our Dockerfile and artifacts, So that in future we can use that location to create our docker images.
- Enter as root user and go the /opt and create a docker directory in it
```
cd /opt
mkdir docker
```
- docker directory is owned by the root, but in the jenkins we have configured that these artifacts are copied by `dockeradmin` user. We need to give the ownership of docker directory to the dockeradmin
```
chown -R dockeradmin:dockeradmin docker
```
- Copy the Dockerfile to `/opt/docker` directory
```
cd ~
mv Dockerfile /opt/docker/
```
- Dockerfile is owned by the root. We need to give the ownership to `dockeradmin`
```
cd /opt/docker/
chown -R dockeradmin:dockeradmin /opt/docker
```
- Now go to the jenkins job and tell the jenkins to not copy the artifacts to the `/home/dockeradmin` in the dockeradmin user. Instead copy it to `/opt/docker`.
```
Remote Directory: //opt//docker
```
- Save and apply and click `Build Now` and now we can see artifact in `/opt/docker` directory. 
- Edit the Dockerfile to put the artifact in the image and deploy it on the tomcat container.
```
cd /opt/docker
vi Dockerfile
```
- Add these instructions in the Dockerfile and save it.
```
FROM tomcat:latest
RUN cp -R /usr/local/tomcat/webapps.dist/* /usr/local/tomcat/webapps
COPY ./*.war /usr/local/tomcat/webapps
```
- Create an image from the Dockerfile
```
docker build -t tomcat:v1 .
```
- Create a container from that image
```
docker run -d --name tomcatv1 -p 8086:8080 tomcat:v1
```
- Now we can access the application which is in the docker container
```
docker_host_public_ipv4_address:8086/webapp
```
![image](https://user-images.githubusercontent.com/56789226/219846573-e98cbdd8-0d36-41d4-94df-55977a35034f.png)

## Automate build and deployment on Docker container
- In the `BuildAndDeployContainer` job go to Configure
```
Post-build Actions: Send build artifacts over SSH
Exec commands:
cd /opt/docker;
docker build -t regapp:v1 .;
docker run -d --name registerapp -p 8087:8080 regapp:v1
```
- First we will stop and delete all the containers and delete all the images
```
cd /opt/docker
docker stop $(docker ps -aq)
docker container prune
docker image prune -a
```
- Now if we click `Build Now` in our Jenkins job it will automatically copy the war file in `/opt/docker` directory and create an image from it and finally create a container and deploy the war file onto the container and start the application. We can access the application on browser by entering `public_ipv4_address:8087/webapp/`
- Now if i make some changes in my repository `index.jsp` code. Jenkins will automatically trigger the job but there will be a error because `registerapp` container which it is creating is already been used by a container which exist already in `/opt/docker` . We cannot create multiple containers with the same name.

## Jenkins job to automate CI/CD to deploy application on docker container
- Go back to `BuildAndDeployContainer` job and click `Configure` and then `Exec command`. Now before creating a new `regsiterapp` container we need to delete existing `registerapp` container.
```
Exec commands:
cd /opt/docker;
docker build -t regapp:v1 .;
docker stop registerapp;
docker rm registerapp;
docker run -d --name registerapp -p 8087:8080 regapp:v1
```
- Now if we click `Build Now` it will deploy war file to docker image and successfully start the container and we can access it from the browser. 
- We will do one more change to `index.jsp` code in repository. Once we make a change in the repository, jenkins will trigger the job automatically and push the war file to the `/opt/docker` and build the Dockerfile and create an image and deploy our application onto the container and we can access it from the browser. All these process will be done in an automated way.
![image](https://user-images.githubusercontent.com/56789226/219847318-087e7435-e170-44a0-9a57-acd73d4fac35.png)

# 5.Integrating Ansible in CI/CD pipeline

## Why do we need Ansible.
- Till now we were using Jenkins as Build and Deployment tool. Now we will use Ansible as a Deployment tool so that jenkins need not to do the administrative kind of activities. Because jenkins work more efficiently as Build tool. Along with ansible we will be using Docker Hub. In this case Jenkins will take the code from github and build artifacts with the help of maven and copy those artifacts onto the ansible server. Now it is ansible task to create image and deploy the container. Ansible with the help of Dockerfile will create a docker image and store it in Docker Hub. And finally when we execute the Ansible playbook to deploy the container, Docker host communicates with docker hub and pulls the image and create a container out of it.
![image](https://user-images.githubusercontent.com/56789226/220830897-bff1dd40-602e-4336-b352-b13a79c938d2.png)

## Ansible Installation
- Setup EC2 Instance
- Setup hostname
- Create ansadmin user
- Add user to sudoers file so that ansadmin user will get administrative privileges.
- Generate ssh keys
- Enable password based login
- Install ansible
<hr>

- First we need to launch an EC2 instance with Keypair 
```
Name: Ansible_Server
AMI: Amazon Linux 2
Instance type: t2.micro
key pair login : use the already generated key pair login during setting up Jenkins_Server
Security group name: Devops_Security_Group
```
- ssh into EC2 instance using
```
ssh -i key_pair.pem ec2-user@public_ipv4_address
```
- Become root user
```
sudo su -
```
- Change the host name and then ssh again to the server.And go back to root user
```
vi /etc/hostname
 ansible-server
init 6
sudo su -
```
- Create a new user
```
useradd ansadmin
```
- Set password for ansadmin user
```
passwd ansadmin
```
- Add this user to the sudoers file
```
visudo 
 ansadmin ALL=(ALL)    NOPASSWD: ALL
```
- We will enable password based authentication and reload service
```
vi /etc/ssh/sshd_config
 PasswordAuthentication yes
 #PasswordAuthentication no
service sshd reload
```
- Create keys for ansadmin user
```
sudo su - ansadmin
ssh-keygen
```
- Install Ansible
```
sudo su -
amazon-linux-extras install ansible2
```
##  Integrate Docker with Ansible
- We will add Docker host to ansible as a managed node, so that ansible control node can manage our docker host.
- On the docker host we need to
1. Create ansadmin
2. Add ansadmin to sudoers file
3. Enable password based login
- On the Ansible Node
1. Add docker host IP address in the host file
2. Copy ssh keys
3. Test connection

- On the docker host system
```
sudo su -
useradd ansadmin
passwd ansadmin
visudo 
  ansadmin ALL=(ALL)  NOPASSWD: ALL
  :wq
# Password based login already enabled no need to enable again
```
- On the ansible server
- Add the docker host as a managed node
```
vi /etc/ansible/hosts
  delete everything and add docker_host private ip address
```
- Switch to ansadmin user and copy the key to the docker host 
```
sudo su - ansadmin
cd .ssh
ssh-copy-id private_ip_docker_host
```
- To check whether ansible server is successfully connected to the docker host we will use the following command. If in the output it shows success then it is successfully connected.
```
ansible all -m ping 
```

## Integrate Ansible with jenkins 
- Go to jenkins server, `Manage jenkins` -> `Configure system`. We need to add below information under `Publish over SSH`.
```
Name: ansible-server
Hostname: private_ip_of_ansible_server
username: ansadmin
Enable user authentication
password:
```
- Now we can create our Jenkins job
```
Name: Copy_Artifacts_onto_Ansible
Copy from: BuildAndDeployContainer
deselect the POLL SCM for now
Post-Build actions:
SSH Server: ansible-server
Remote directory: //opt//docker
Delete Exec command
```
- But in ansible-server we don't have `/opt/docker` directory, so we need to create one. And then save and build the jenkins job. Once we build the job, Jenkins will build the code using maven and deploy the war file onto the ansible server `/opt/docker` directory.
```
sudo su - ansadmin
cd /opt
sudo mkdir docker
sudo chown ansadmin:ansadmin docker
```
## Build an image and create container on Ansible
- On the ansible server install docker 
```
sudo yum install docker -y
```
- Add the ansadmin to docker group
```
sudo usermod -aG docker ansadmin
```
- Start the docker services
```
sudo service docker start
```
- To create a docker image we need to create a Dockerfile and build that Dockerfile
```
cd /opt/docker
vi Dockerfile
  FROM tomcat:latest
  RUN cp -R /usr/local/tomcat/webapps.dist/* /usr/local/tomcat/webapps
  COPY ./*.war /usr/local/tomcat/webapps
sudo chmod 777 /var/run/docker.sock 
docker build -t regapp:v1 .
```
- Create a container from docker image and run it in terminal. 
```
docker run -t --name regapp_server -p 8081:8080 regapp:v1
```
- Now we can access the application on the browser using `ansible_public_address:8081/webapp/`.Till now we were executing the commands manually on ansible, now we will create an ansible playbook and do all these activities through playbook.

## Ansible playbook to create image and container

- On the ansible server
```
sudo su - ansadmin
cd /opt/docker
```
- We want to execute the ansible playbook on the ansible server, so we need to add the private IP of ansible server in /etc/ansible/hosts which is a inventory file.
```
sudo vi /etc/ansible/hosts
  [dockerhost]
  private_ip_dockerhost 
  [ansible]
  private_ip_ansible_server
```
- We need to copy the ssh key on the ansible server itself to communicate with it through playbook
```
ssh-copy-id private_ip_ansible_server
```
- We will go inside `/opt/docker` and create an ansible playbook there.
```
cd /opt/docker
vi regapp.yml
```
- In ansible playbook
``` yaml
---
- hosts: ansible
  
  tasks:
  - name: create docker image
    command: docker build -t regapp:latest .
    args:
      chdir: /opt/docker
```
- To check the ansible playbook we will use
```
ansible-playbook regapp.yml --check
```
- To execute ansible playbook
```
ansible-playbook regapp.yml
```
- This is how we can create docker image using ansible playbook

## Copy image on to docker hub 
- Create an account on docker hub
- To login to docker hub from ansible server use
```
docker login
Username:
password:
```
- To push the image onto to the docker hub
```
docker tag image_id username/regapp:latest
docker push username/regapp:latest
```

## Jenkins job to build an image onto ansible
- On the ansible server in `/opt/docker` directory
```
vi regapp.yml
```
- Edit the `regapp.yml` file to add two more task. Tagging and pushing the image onto the dockerhub
``` yaml
---
- hosts: ansible

  tasks:
  - name: create docker image
    command: docker build -t regapp:latest .
    args:
      chdir: /opt/docker
        
  - name: create tag to push image onto dockerhub
    command: docker tag regapp:latest username/regapp:latest
      
  - name: push docker image
    command: docker push username/regapp:latest
```
- Check the ansible playbook using 
```
ansible-playbook regapp.yml --check
```
- To execute the ansible playbook only on ansible server even if many IP address exist in ansible group, we will use
```
ansible-playbook regapp.yml --limit ansible_server_private_ip
```
- We can give above command to jenkins server, so jenkins server will initiate ansible playbook whenever there is a new code commit.
- In the `Copy_Artifacts_onto_Ansible` job go to `Configure`
```
Poll SCM : * * * * *
ssh-server: ansible_server
Exec command: ansible-playbook /opt/docker/regapp.yml
```
- Now when we update the source code, jenkins trigger the job, copy artifacts onto ansible, ansible create an image and deploy that image onto docker hub.
![image](https://user-images.githubusercontent.com/56789226/220843792-25138fc8-0274-4d1e-af01-9ba0543b7b89.png)

## How to create container on dockerhost using ansible playbook
- We will create an ansible playbook in `/opt/docker` to create a container.
```
vi deploy_regapp.yml
```
- In ansible playbook
``` yaml
---
- hosts: dockerhost
  
  tasks:
  - name: create container
    command: docker run -d --name regapp_server -p 8082:8080 username/regapp:latest
```
- To check ansible playbook
```
ansible-playbook deploy_regapp.yml --check
```
- Login into the Docker_Host server and become root user and delete the docker images and containers
```
docker stop container_id
docker rm container_id
docker image prune -a
chmod 777 /var/run/docker.sock
```
- Once we execute the ansible playbook on the ansible server, it will pull the docker image from docker hub and create a container on docker host
```
ansible-playbook deploy_regapp.yml
```
- We can access the application on the browser using `docker_host_public_ip:8082/webapp`
![image](https://user-images.githubusercontent.com/56789226/220857138-653705c4-889c-449a-9d27-14b92c05203b.png)
- But when we execute ansible playbook again it gives error because the `regapp_server` container name is already been used by the container.

## Continuous deployment of docker container using ansible playbook
- Remove existing container
- Remove existing image
- Create new container
```
vi deploy_regapp.yml
```
- Edit the ansible playbook
``` yaml
---
- hosts: dockerhost

  tasks:
  - name: stop existing container
    command: docker stop regapp_server
    ignore_errors: yes

  - name: remove the container
    command: docker rm regapp_server
    ignore_errors: yes

  - name: remove image
    command: docker rmi username/regapp:latest
    ignore_errors: yes

  - name: create container
    command: docker run -d --name regapp_server -p 8082:8080 username/regapp:latest
```
- To check ansible playbook
```
ansible-playbook deploy_regapp.yml --check
```
- Execute ansible playbook
```
ansible-playbook deploy_regapp.yml
```

## Jenkins CI/CD to deploy on container using Ansible
- We are manually executing `deploy_regapp.yml` file. Now using jenkins we will automate everything.
- Go to the jenkins dashboard -> `Copy_Artifacts_onto_Ansible` job -> `Configure`
```
Exec commands:
ansible-playbook /opt/docker/regapp.yml;
sleep 10;
ansible-playbook /opt/docker/deploy_regapp.yml
```
- Disable Poll SCM for `BuildAndDeployContainer` or else while executing our job it will create two containers.
- Once we make changes in source code. `Copy_Artifacts_onto_Ansible` job will get trigger and build the code, create an image, push that image to dockerhub, pull the latest image from dockerhub onto dockerhost and create a container and run our application in it. 
- Application which is in a container can be accessed from browser using `dockerhost_public_ip:8082/webapp`
![image](https://user-images.githubusercontent.com/56789226/220859548-780f0c0e-f823-44b3-acd1-c98c46629eee.png)
- But, whenever there are changes, we are terminating the existing container and creating new one, during this time end user is not able access the application. That is where container management system comes into picture. In next section we will leverage container management system to run our containerized application with high availability and fault tolerance.

# 6.Kubernetes on AWS

## Why Kubernetes 
- Till now we are able to deploy our application successfully on a docker container. But if our docker container goes down there is no way to recover it. To overcome this problem we can use container management service called kubernetes. We will deploy our application as pod on kubernetes environment. 

## Setup bootstrap server for eksctl
- We will setup bootstrap image to create cluster
- Create an EC2 instance
```
Name: EKS_Bootstrap_server
AMI: Amazon Linux 2
Instance type: t2.micro
key pair login : use the already generated key pair login during setting up Jenkins_Server
Security group name: Devops_Security_Group
```
- ssh into the EC2 instance and become root user
```
ssh -i key_pair.pem ec2-user@public_ipv4_address
sudo su -
```
- Install the latest AWS CLI
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```
- Install kubectl
```
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.21.14/2023-01-30/bin/linux/amd64/kubectl
```
- Add execution permission to kubectl
```
chmod +x kubectl
```
- Move kubectl to `/usr/local/bin`
```
mv kubectl /usr/local/bin
```
- Install eksctl and move it to `/usr/local/bin`
```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
cd /tmp
mv eksctl /usr/local/bin
```
- Create an IAM role and attach it to EC2 instance.
- Search `IAM` click on `Roles` and select `Create role`.
```
Trusted entity type: AWS service
Common use cases: EC2
Add permissions:
AmazonEC2FullAccess
AWSCloudFormationFullAccess
IAMFullAccess
AdministratorAccess (not recommended)
Role name: eksctl_role
```
- Add this role to EC2 instance `EKS_Bootstrap_server`. Got to `Actions` -> `Security` -> `Modify IAM`  and select `eksctl_role`.

## Setup Kubernetes using eksctl
- Setup kubernetes cluster on EKS
```
cd /tmp
eksctl create cluster --name valaxy \
--region ap-south-1 \
--node-type t2.small
```
- Two t2.small instances have been created 
- To check nodes
```
kubectl get nodes
```
- To check all the resources
```
kubectl get all
```
- To create pod
```
kubectl run webapp --image=httpd
```
- To check pod
```
kubectl get pod
```
- We have created a pod using kubectl run command. But in actual scenario we will use manifest file. We will see how to create resources with the help of manifest file.

## Run Kubernetes basic commands
- Example - Deploy Nginx Container
```
kubectl create deployment demo-nginx --image=nginx --replicas=2 --port=80
```
- To check deployment
```
kubectl get deployment
```
- Expose the deployment as service. This will create an ELB in front of those 2 containers and allow us to publicly access them
```
kubectl expose deployment demo-nginx --port=80 --type=LoadBalancer
```
- We can access the nginx application from browser using `EXTERNAL-IP` which can be accessed using `kubectl get service`
- We have deployed our application through command line, but we should use manifest file, which we will see in next section.

## Create 1st manifest file
- First delete deployment (deletes pods and replicaset as well) and service
```
kubectl delete deployment demo-nginx
kubectl delete service/demo-nginx
```
- Create manifest file
```
vi pod.yml
```
- In manifest file create pod, and run container in it on port 80
``` yaml
apiVersion: v1
kind: Pod
metadata:
  name: demo-pod
# labels:
#   app: demo-app

spec:
  containers:
    - name: demo-nginx
      image: nginx
      ports:
        - name: demo-nginx
          containerPort: 80
```

##  Create a service manifest file
- To expose our pod we need to create a service
```
vi service.yml
```
- In service file expose the pod on port 80
``` yaml
apiVersion: v1
kind: Service
metadata:
  name: demo-service

spec:
  ports:
  - name: nginx-port
    port: 80
    targetPort: 80
      
  type: LoadBalancer
```
- To create a pod with the help of manifest file we use the following command
```
kubectl apply -f pod.yml
```
- To create a service with the help of manifest file 
```
kubectl apply -f service.yml
```
- If we use External-IP in the browser it will not work because the instances are out of service, as service don't know where the request should be forwarded. We will use labels and selectors so that it knows which pod to forward the request to.
- In pod.yml manifest file add labels
``` yaml
apiVersion: v1
kind: Pod
metadata:
  name: demo-pod
  labels:
    app: demo-app

spec:
  containers:
    - name: demo-nginx
      image: nginx
      ports:
        - name: demo-nginx
          containerPort: 80
```
- In service.yml add selector with the same label name which we used in pod.yml so it knows which pod to forward the request.
``` yaml
apiVersion: v1
kind: Service
metadata:
  name: demo-service

spec:
  ports:
  - name: nginx-port
    port: 80
    targetPort: 80

  selector:
    app: demo-app

  type: LoadBalancer
```
- Update the pod.yml and service.yml manifest file
```
kubectl apply -f pod.yml
kubectl apply -f service.yml
```
- Now if we use the `EXTERNAL-IP` in the browser we can see nginx page.
![image](https://user-images.githubusercontent.com/56789226/221411681-021600db-e8d7-40c5-bd99-a0ac9943c085.png)

# 7.Integrating Kubernetes in CI/CD pipeline

## Write a deployment file
- We will first delete the existing pod and service
```
kubectl delete pod demo-pod
kubectl delete service/demo-service
```
- If the pod gets deleted or terminated there is no way it is going to recreate by default. How to overcome this problem? We need to use deployment in manifest file.

## Use deployment and service files to create and access pod
- Create a deployment file
```
vi regapp-deployment.yml
```
- In the deployment file 
``` yaml
apiVersion: apps/v1 
kind: Deployment
metadata:
  name: valaxy-regapp
  labels: 
     app: regapp

spec:
  replicas: 3 
  selector:
    matchLabels:
      app: regapp

  template:
    metadata:
      labels:
        app: regapp
    spec:
      containers:
      - name: regapp
        image: username/regapp
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
```
- Create a service file
```
vi regapp-service.yml
```
- In service file
``` yaml
apiVersion: v1
kind: Service
metadata:
  name: valaxy-service
  labels:
    app: regapp 
spec:
  selector:
    app: regapp 

  ports:
    - port: 8080
      targetPort: 8080

  type: LoadBalancer
```
- To create pod, deployment and replica set from deployment file use
```
kubectl apply -f regapp-deployment.yml
```
- To execute service file and expose the pods using LoadBalancer we use 
```
kubectl apply -f regapp-service.yml
```
- Now if we use  `EXTERNAL-IP:8080/webapp` we can access our application. We have successfully deployed our application on pods.
- If we terminate a pod it can automatically provision a new pod because the replicaset makes sure that desired state of pod is running.
- But we are executing kubectl command manually, now we will integrate kubernetes with ansible so that ansible playbook can execute kubectl commands automatically when we want to deploy our latest application on Kubernetes cluster.

## Integrate Kubernetes bootstrap server with Ansible 
1. **On the bootstrap server**
- Create ansadmin
- Add ansadmin to sudoers files
- Enable password based login
<br>

2. **On the ansible server**
- Add bootstrap server to host file
- copy ssh keys
- Test the connection
<br>

- In the ansible server
- ssh into the ansible server
```
ssh -i key_pair.pem ec2-user@public_ipv4_address
```
- On the bootstrap server create ansadmin user and add him to sudoers file
```
useradd ansadmin
passwd ansadmin
visudo
  ansadmin ALL=(ALL)   NOPASSWD: ALL
```
- Enable password based authentication
```
vi /etc/ssh/sshd_config
  PasswordAuthentication yes
  #PasswordAuthentication no
```
- Reload the service
```
service sshd reload
```
- Go back to the ansible server
```
sudo su -
sudo su - ansadmin
cd /opt/docker
mv regapp.yml create_image_regapp.yml
```
- In the `deploy_regapp.yml` host is dockerhost because we were deploying container on dockerhost. Now the host will change because we are going to deploy it on kubernetes as a pod. We will change the yml file name so that it is more meaningful
```
mv deploy_regapp.yml docker_deployment.yml
```
- Add bootstrap image to host file
```
vi hosts
  [kubernetes]
  bootstrap_server_private_ip
  [ansible]
  ansible_server_private_ip
```
- copy ssh-id to bootstrap_server
```
ssh-copy-id bootstrap_server_private_ip
```
- To check
```
ansible -i hosts all -a uptime
```

## Create ansible playbooks for deploy and service file
- In the ansible server create ansible playbook to execute deployment and service file
- Create ansible playbook to execute deployment file
```
vi kube_deploy.yml
```
- In this file 
``` yaml
---
- hosts: kubernetes
# become: true
  user: root  

  tasks:
  - name: deploy regapp on kubernetes
    command: kubectl apply -f regapp-deployment.yml
```
- Create ansible playbook to execute service file
```
vi kube_service.yml
```
- In this file
``` yaml
---
- hosts: kubernetes
# become: true
  user: root
  
  tasks:
  - name: deploy regapp on kubernetes
    command: kubectl apply -f regapp-service.yml
```
- Before executing ansible-playbook in the bootstrap_server delete service, deployment
```
kubectl delete -f regapp-service.yml
kubectl delete -f regapp-deployment.yml
```
- In the ansible server execute playbook
```
ssh-copy-id root@bootstrap_server
ansible-playbook -i /opt/docker/hosts kube_deploy.yml
ansible-playbook -i /opt/docker/hosts kube_service.yml
```
- Once we execute these playbooks deployment, pod and service is created.

## Create jenkins deployment job for Kubernetes
- Create a new job
```
Item name: Deploy_On_Kubernetes
Freestyle Project
Description: Deploy On Kubernetes
Post Build Actions: Send build artifacts over ssh
Name: ansible-server
Exec commad:
ansible-playbook -i /opt/docker/hosts /opt/docker/kube_deploy.yml;
ansible-playbook -i /opt/docker/hosts /opt/docker/kube_service.yml
```
- If we build the job, it will initialize the ansible-playbook on the ansible server and ansible is going to initialize the deployment as well as service file of kubernetes cluster.
- Instead of running two different playbook we can merge it in one.
```
vi kube_deploy.yml
```
- In this file
``` yaml
---
- hosts: kubernetes
# become: true
  user: root

  tasks:
  - name: deploy regapp on kubernetes
    command: kubectl apply -f regapp-deployment.yml

  - name: create service for regapp
    command: kubectl apply -f regapp-service.yml
```
- Accordingly change Exec commands in Jenkins job
```
Exec command:
ansible-playbook -i /opt/docker/hosts /opt/docker/kube_deploy.yml;
```

## CI Job to create Image for Kubernetes.
- Our intention is to update the docker image by changing the source code, so that the kubernetes should deploy with latest code.
- Rename `Deploy_On_Kubernetes` job to `RegApp_CD_Job`
- Create CI job so that when code is changed, it will build the code and create docker image on ansible and push that docker image to dockerhub.
```
Item Name: RegApp_CI_Job
Copy from: Copy_Artifacts_onto_Ansible
Description: Build code with help of maven and create an image on ansible and push it onto dockerhub
Exec commands: 
ansible-playbook /opt/docker/create_image_regapp.yml;
```
- Disable Poll SCM for `Copy_Artifacts_onto_Ansible`
- Now when we make changes in the source code,  the ansible playbook is executed, docker image is created on ansible server and that image is pushed to docker hub.

##  Enabling rolling update to create pod from latest docker image
- In the CI job there is option to initialize another jenkins job(CD job). So once CI job is executed CD job will take that latest docker image and do the deployment on kubernetes.
- Go to the `RegApp_CI_Job` -> `Configure`
```
Add Post Build Action: Build other project
Projects to build: RegApp_CD_Job
Trigger only if Build is stable
```
- So once CI job is executed, then CD job will execute successfully. But once we make changes in the source code, latest docker image is pushed successfully onto docker hub. But new deployment is not created in kubernetes cluster.
- We need to add rollout restart command in `kube_deploy.yml` file
- In the ansible server go to `kube_deploy.yml` file and make this changes
``` yaml
---
- hosts: kubernetes
# become: true
  user: root

  tasks:
  - name: deploy regapp on kubernetes
    command: kubectl apply -f regapp-deployment.yml

  - name: create service for regapp
    command: kubectl apply -f regapp-service.yml
      
  - name: update deployment with new pods if image update in docker hub
    command: kubectl rollout restart deployment.apps/valaxy-regapp
```

## Complete CI and CD job to build and deploy code on Kubernetes
- Finally our entire CI/CD pipeline is setup. Now whenever we commit the new code onto github, Jenkins CI job pull the code, builds it and pushes the war file onto the ansible server and a docker image is created. That docker image is pushed onto the docker hub. Then CD job starts executing. And rolling update starts and new pods are created in kubernetes cluster. We can access the latest application using `External-IP:8080/webapp` in our browser.
![image](https://user-images.githubusercontent.com/56789226/221557047-943145f6-4d1d-402e-b3c8-f98625b4fd02.png)

## Clean up kubernetes cluster
```
kubectl delete deployment.apps/valaxy-regapp
kubectl delete service/valaxy-service
eksctl delete cluster valaxy --region ap-south-1
terminate all the instances
```
























































