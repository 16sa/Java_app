# Docker Containerisation of Java App
Pre-requisites:
--------
    - Create the EC2 instance with T2.MICRO and UBUNTU from AWS console Management, Use EC2 Instance Connect to Access it
    - Install Maven
	apt update -y
	apt install maven -y
	mvn -version
    - Install Docker
	apt install apt-transport-https ca-certificates curl software-properties-common -y
	curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
	sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable" -y
	apt update -y
	apt-cache policy docker-ce
	apt install docker-ce -y
	chmod 777 /var/run/docker.sock
     - Install JDK 8
	apt update
	apt install openjdk-8-jdk
     ==> Set the JAVA_HOME Environment Variable using this command: export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
     ==> Make it permanent using this command: echo "export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64" >> ~/.bashrc

Clone the code in EC2 in /opt folder
-------
    cd /opt
    git clone https://github.com/16sa/Java_app.git
    
Build Maven Artifact:
-------
Go to the folder Java_app and execute the below command:

    mvn clean install -DskipTests

==> Check inside the Target folder for the jar
 
Build Docker image for the Application
--------------
Go over the Dockerfile cd Java_app and build the doocker image using this command:

    docker build -t your_docker_hub_username/IMAGE_name:tag .
    docker images
  
Docker login
-------------
To configure your EC2 instance so that Docker running on it can push images to your Docker Hub repository, you need to log in to Docker Hub from that EC2 instance:

    docker login
    
Push docker image to dockerhub
-----------
    docker push your_docker_hub_username/IMAGE_name:tag 
    
Create the container:
--------
    docker run -d -p 8080:8080 your_docker_hub_username/IMAGE_name:tag
    docker ps ( to check all running and non running containers you can use docker ps -a command)
    
Check the logs of container:
-------
    docker logs <Container ID>

Now Go inside the container and check for JAR file under / path
--------
    docker exec -it <Container ID> /bin/sh

Open the Security Groups to All traffic in EC2 instance and hit the below URL
--------
http://EC2_PUBLIC_IP:8080/home/data

Terminate the instance
--------
