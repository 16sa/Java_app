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
    docker build -t vikashashoke/kubernetes-configmap-reload .
  
Docker login
-------------
    docker login
    
Push docker image to dockerhub
-----------
    docker push vikashashoke/kubernetes-configmap-reload
    
Deploy Spring Application:
--------
    kubectl apply -f kubernetes-configmap.yml
    
Check Deployments, Pods and Services:
-------

    kubectl get deploy
    kubectl get pods
    kubectl get svc
    
Now Goto Loadbalancer and check whether service comes Inservice or not, If it comes Inservice copy DNS Name of Loadbalancer and Give in WebUI

    http://a70a89c22e06f49f3ba2b3270e974e29-1311314938.us-west-2.elb.amazonaws.com:8080/home/data
    
![2](https://user-images.githubusercontent.com/63221837/82123471-44f5f300-97b7-11ea-9d10-438cf9cc98a0.png)

Now we can cleanup by using below commands:
--------
    kubectl delete deploy kubernetes-configmap-reload
    kubectl delete svc kubernetes-configmap-reload
# springboot_k8s_application
# mrdevops_java_app
