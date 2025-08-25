CI PIPELINE SETUP WITH VARIOUS TOOLS

Step 1 – Create the EC2 instance in AWS account with these parameters:

EC2 type – Ubuntu t2.large
EBS volume – 30 GB
[ from 8 GB –> 30 GB ] and t2.micro -> t2.large [ to have More memory and more cpu ]
Region – eu-west-3 [optional]
Create a new key pair for ssh and name it
use the ssh key pair to connect to ec2 instance using this command: 
ssh -i /path/key-pair-name.pem instance-user-name@instance-public-dns-name
switch to root use using sudo su - command

Step 3 – Install JAVA/Jenkins on EC2

sudo apt update -y

sudo apt upgrade -y 

sudo apt install openjdk-17-jre -y

curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update -y 
sudo apt-get install jenkins -y


**** Change the security group of ec2 instance and add all traffic in inbound rules
**** Sign Into Jenkins console http://<EC2_PUBLIC _IP>:8080/
**** Get the Administrator password by hitting the below command in EC2
cat /var/lib/jenkins/secrets/initialAdminPassword
And add it to the console to unlock jenkins
 

Step 4 – Install Docker on EC2

sudo apt update -y

sudo apt install apt-transport-https ca-certificates curl software-properties-common -y

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable" -y

sudo apt update -y

apt-cache policy docker-ce 

sudo apt install docker-ce -y

sudo systemctl status docker

sudo chmod 777 /var/run/docker.sock

sudo usermod -aG docker jenkins

sudo systemctl restart docker

sudo systemctl restart jenkins


Step 5 – Install Sonarqube on EC2

SonarQube is an open-source platform developed by Sonar Source for continuous
inspection of code quality to perform automatic reviews with static analysis 
of code to detect bugs, code smells, and security vulnerabilities on 20+
programming languages.

Quality Profiles are a core component of SonarQube where you define sets of Rules 
that, when violated, raise issues on your codebase SonarQube executes rules on source
code to generate issues. There are four types of rules: Code Smell (Maintainability
domain); Bug (Reliability domain); Vulnerability (Security domain); Security Hotspot 
(Security domain)

Quality Gate: is a set of conditions the project must meet before it can be released

docker run -d --name sonarqube -p 9000:9000 -p 9092:9092 sonarqube
docker ps 

**** Start docker container if it's not already up
docker ps -a [ Get the container ID ]
docker start <containerID>

Step 6 – Install Maven on EC2

sudo apt update -y
sudo apt install maven -y
mvn -version

Reboot EC2 instance
Start docker container if it's not already up

Step 7 – Install Trivy Application on EC2: Trivy is a Simple and Comprehensive Vulnerability Scanner 
for Containers and other Artifacts, Suitable for CI.

sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy

Step 8 – Install Jfrog on EC2

sudo usermod -aG docker $USER
docker pull docker.bintray.io/jfrog/artifactory-oss:latest
sudo mkdir -p /jfrog/artifactory
mkdir -p /opt/jfrog/artifactory/var/etc/security
openssl rand -hex 16 > /opt/jfrog/artifactory/var/etc/security/master.key
sudo chown -R 1030 /jfrog/

docker run --name artifactory -d \
  -p 8081:8081 \
  -p 8082:8082 \
  -v /jfrog/artifactory:/var/opt/jfrog/artifactory \
  docker.bintray.io/jfrog/artifactory-oss:latest

open this file /jfrog/artifactory/etc/system.yaml in your EC2 instance , update this setting

shared:
  database:
    allowNonPostgresql: true
	
docker ps 

docker stop [artifactory_container_id]
docker start [artifactory_container_id]
docker logs [artifactory_container_id]

Access Jfrog URL using this link http://<IP Address>:8082/artifactory/

Log into the Artifactory, using the following default credentials: Username - admin. 
Password - password ; reset your password

Add jfrog url http//<IP ADDRESS>:8082


Step 9 – Install Git on EC2

sudo apt update
sudo apt install git
git --version

Step 10 – Install all suggested plugins from jenkins console

Step 11 – Create first user admin

Step 12 – Install the Needed plugins for Sonar and Jfrog

Dashboard -> Manage Jenkins -> Plugins -> Available plugins

Sonar Gerrit
SonarQube Scanner
SonarQube Generic Coverage
Sonar Quality Gates
Quality Gates
Artifactory
Jfrog

Step 13 - Integrate All tools with Jenkins

**** Login to Jenkins: http://<IP ADDRESS>:8080
**** Adding Jenkins Shared Library

Go to → Manage Jenkins → System and add the below data in Jenkins Global Trusted Pipeline Libraries:

Name: jenkins_shared_library
Default Version: main
Project Repository: https://github.com/<YOUR SHARED LIB>/jenkins_shared_library.git

**** Login into sonar dashboard using Username – admin and Password – admin ; reset your password
http//<IP ADDRESS>:9000

**** Create Sonar token for Jenkins Sonar Dashboard -> Administration -> My Account -> Security -> 
Create token -> Name: Jenkins , Type: Global Analysis Token -> Generate ->
Save the token to some text file
**** Configure Jenkins Credentials for docker hub and Sonar
manage Jenkins → Credentials → System → Global Credentials → 
Add Credentials → Secret Text → Secret: paste the secret token, ID: sonarqube-api
Add Credentials → username with password → add username (Docker Hub User Name) → 
password (Docker Hub) → ID: docker.

**** Adding Sonarqube

Go to → Manage Jenkins → System and add the below data in SonarQube Servers:

Click on sonarqube servers -> add name:sonarqube-api and url http//<IP ADDRESS>:9000 -> Click on
add token -> Select the sonar token from previous step sonarqube-api


Step 14 – Create a pipeline Job named demo for example and Add pipeline script as SCM

New Item → name, pipeline → ok
go to configure → select pipeline → select pipeline script with scm → SCM: git → paste your git repo → 
change branch name with main or any other branch → save



















