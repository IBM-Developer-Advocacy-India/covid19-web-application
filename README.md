# Covid-19 Data Analytic Microservices Application with Kubernetes and OpenShift

We have seen a range data published on the impact of various parameters on the spread of covid-19, including population density, average number of people per household, ethnicity, weather data etc.
Have you ever wanted to run your own analytics on covid-19 data, and examine data sets in order to draw a particular conclusion? Or possibly evaluate a theory, that may or not may not be true. Such analytics could potentially shed light on the impacts of various factors, and you can apply them to a variety of problems.   
Maybe you'd like to see the impact of temperature and humidity on the spread of covid-19 in different countries?  

This is a multipart workshop series on building,
deploying and managing microservices applications with Kubernetes and openshift.

Our application also comes with a frontend [User Interface](https://github.com/IBM-Developer-Advocacy-India/covid-19-ui) that connects to our parsers and invokes the API endpoints to display data and showcase the power of microservices running as conainers on Kubernetes and Openshift.  


This application has been designed as a template for designing your own analytical microservices and deploying onto Kubernetes.

## Table Of Contents

This workshop series will be focused on:

Part 1: Cloud Native Development, Microservices and the Architecture of our Covid-19 Data Parser  
Part 2: Build your Microservice container with Docker
Part 3: Deploy and manage your application with Kubernetes
Part 4: Deploy and manage your application with OpenShift on IBM Cloud  
Part 5: Build, Deploy and Share with CodeReady Workspaces  
Part 6: Build and Test your application with CodeReady Containers
Part 7: Build your CI/CD pipelines with Jenkins and Tekton

Here is what you will learn by the end of this workshop series:  

![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide2.png)
---
## Part 1: Cloud Native Development, Microservices and the Architecture of our Covid-19 Data Parser  


### Agenda  

In this section you will learn:
- Introduction to this workshop series
- Cloud Native Application Development
  - Advantages
- Microservices
  - Why microservices?
  - Monolithic Applications
- An overview of Covid-19 data analytic web application
  - Quick summary
  - Data source & format
  - Data Parser
  - REST APIs endpoints
- Application Demo  


## Prerequisites

Spring Boot v2.2 - https://spring.io/guides/gs/spring-boot/

OpenJDK v11 - https://openjdk.java.net/install/

(Optional) Apache Netbeans IDE v12 - https://netbeans.apache.org/download/

Node.js v14 - https://nodejs.org/en/download/

Docker Latest - https://docs.docker.com/engine/install/

Minikube Latest - https://kubernetes.io/docs/tasks/tools/install-minikube/

CodeReady Containers - https://developers.redhat.com/products/codeready-containers

(Optional) OpenShift v4.3 on IBM Cloud - https://www.ibm.com/cloud/openshift


By the end of this series, you'll have 4 x containerised microservices deployed and running on Kubernetes, OpenShift cluster, CodeReady Containers, CodeReady Workspaces.
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide17.png)  

Those Microservices are:

- Data Parser written in Java.
- UI frontend written in Java to generate HTML and Node.js.
- Analytical application wrtittn in Python Flask.  
- Data Visulization application written in Node.js  

We will be using the data from [COVID-19 Data Repository by the Center for Systems Science and Engineering (CSSE) at Johns Hopkins University](https://github.com/CSSEGISandData/COVID-19)

## Part 2: Build your Microservice container with Docker.  


  
### Agenda
In this section you will learn:
- Install/download prerequisites 
- Package Java Maven application
- Test Java application
- Docker
  - Dockerfile
  - Build Docker image
  - Run Docker containers
  - Use Kubernetes Docker daemon
  - Docker Registry 
  - SSH into Docker images
  - Connecting Docker containers
  - Inspect Docker Containers

![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide26.png)
  

![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide32.png)
  
Let's start by clining the repos and packaging our Java application with Maven:

### Clone The Repositories
```
git clone github.com/IBM-Developer-Advocacy-India/covid19-web-application
git clone github.com/IBM-Developer-Advocacy-India/covid-19-ui
```
### Package Spring Boot with Maven

```bash
mvn clean install 
```
Run the jar file to test the Spring Boot application:
```bash
java -jar target/[filename].jar 
```
Data Parser runs on port 8082. if you want to change th *Port Number*, you need to edit ***"application.properties"*** file under src/main/java/resources/

```bash
curl http://localhost:8082 
```
Now we've ogot our application ready to be containerised with Docker. Before we dive deeper into Docker, let's explore what containers are and how docker fits in containerisation technology.  



![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide35.png)

### Docker Image vs. Docker Container
> Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings. (only interacting with designated resources)

> Container ****<ins>images become containers at runtime</ins>**** and in the case of Docker containers - images become containers when they run on Docker.  

So let's get started and build our first container image with Docker. 
> The first step is to craft our dockerfile and the Dockerfile is essentially the build instructions to build the image. 



![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide36.png)
#### What is a Dockerfile?
A set of build instructions to build the image in a file called "dockerfile".  
#### Craft your Dockerfile
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide40.png)



In the case of our Data Parser Spring Boot application: 
```
FROM adoptopenjdk/openjdk11:latest
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]

```
Dockerfile for Node.js application:
```
FROM node:12
COPY package*.json ./
RUN npm install
ENTRYPOINT [”node",”app.js"]
```
Dockerfile for Python application:
```
FROM python:3
COPY package.py ./
RUN pip install pystrich
ENTRYPOINT [”python",”./app.py"]
```
save the file as <ins>dockerfile</ins> with no file extension.

### Building Docker Image from the Dockerfile
```bash
docker build -t [image name:v1] [path]
```
in this case, let's call it myapp:v1
```bash
docker build -t myapp:v1 .
```
let's take a look at our docker images: 
```
docker images
```
our image must be listed there.  
now let's a look at running containers: 
```
docker ps
```
if you add -al, you can view all running and stopped containers
```
docker ps -al 
```
Here's the command for running the docker container
```
docker run -p [PortHost:PortContainer] [imageName] -d --rm 
```
Now let's go ahead and run our container on port 8082:
```
docker run -p 8082:8082 myapp:v1 -d 
```
-d and --rm flags will respectively run the docker in detached, mode and replace an existing docker image of the same name with the name one.  
We can ping the application by invoking the /hello/ REST endpoint:
```
curl localhost:8082/hello/ 
```
#### Build and Run the UI App

The UI application can be retrieved from here:
<https://github.com/IBM-Developer-Advocacy-India/covid-19-ui>

Now let's build the UI app and call it myui:v1
Dockerfile is the same as the one we used for Data Parser app but changing the name to ***"myui"***
```bash
docker build -t myui:v1 .
```
> in case you haven't run the maven build and packaged the UI App, run this where mvnm file is located
```
mvn clean install 
```
Now let's run the UI app on port 8081:
```
docker run -p 8082:8082 myapp:v1 -d 
```
Open your browser and navigate to 
```
localhost:8081
```
From the UI, click on connect on the top left hand corner and enter:
```
http://localhost:8082
```

As you may have seen, you got an error indicating that the server is not responding. 
There reason is, we can connect to containers directly thorugh Docker, but docker containers cannot discover or comunicate with each other.
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide45.png)

now let's try to ssh into our one of the docker containers and try to connect to the other one to identify the problem.
To simulate the issue that we've just expereinced with the UI app, let's ssh into our UI and try to connect to our data parser from within that container. 
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide46.png)

```
docker exec [container name/ID] -it
```
Here how we ssh into UI app
```
docker exec -it myui:v1 /bin/bash 
```
Now let's connect from within the container and see if it works
```
curl localhost:8082/hello/ 
```
As you can see that doesn't work either.  
> containers need to be connected to the same network in order to communicate with each other

You can inspect your container to investigate the matter by looking for the network wihtin both containers.

```
docker inspect [container name]
```
As you can see our UI and Parser apps are not part of the same network. 
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide48.png)

Let's create a network and instruct our containers to connect to it

```
docker network create test 
```

let's stop our docker containers:

```
docker stop [container id]
```

Let's run our containers again, this time instructing them to join the new network we've just created

```
docker run -p [PortHost:PortContainer] [imageName] --net=test
```

Run UI application on test network:

```
docker run -p 8081:8081 myui:v1 --net=test
```

Run parser application on test network:

```
docker run -p 8082:8082 myapp:v1 --net=test
```

Let's inspect our containers again and get their IP addresses based on thier new network

```
docker inspect [container name/ID]
```

if we try to ping our applications again, they should work fine.  
Go ahead and connect to the parser form the UI app to verify that. 

In the next part we will be using minikube to spin up a single node kubernetes cluster. If we build all our images on your host docker machine, it'd be quite difficult to transfer your images from your host into minikube.  
one solution is to use minikube's docker daemon to build your docker images.  
> you need to set your environmental parameter to use miinkube docker. This command will let you do that:
```
eval $(minikube docker-env) 
```
This step is not needed here, is intended to let you know what we will use minikube's docker.
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide55.png)

## Part 3: Deploy, Run and Maange your Docker Containers with Kubernetes.  
  
<div align="center">
      <a href="https://youtu.be/t8iKnYV3E94">
         <img src="https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Part3-youtube.png">
      </a>
</div>  
  
### Agenda
In this section you will learn:
- Why Kubernetes
- Kubernetes concepts/components
- Deploy on Kubernetes 
  - Minikube
  - Pulling image from registry
  - Create deployment
  - Expose deployment
  - Create services
- Manage with Kubernetes
  - Replicasets
  - Rolling out updates
  - Autoscaling
---
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide60.png)

### Kubernetes

Kubernetes is Greek for helmsman or pilot, hence the helm in the Kubernetes logo. 
Imagine a ship full of containers like in this photo, and the helmsman is to make 
sure the ship sails smoothly through the oceans, and despite all the tides and 
waves, it makes it to the destination safely. the helmsman orders his crew to evenly 
distribute the containers around the ship in a way that, proper balance is struck, no 
one side is abnormally heavier, containers won't fall off, and the ship sails smoothly 
throughout the journey. 
Just like the helmsman, Kubernetes looks after a large number of containerised 
applications, by orchestrating them according to the load, and the available 
underlying resources, making sure our system achieves minimum zero downtime 
and our applications are always up and running. 
In the first and second labs we learned about the advantages and motivations for 
moving away from Monolithic applications and adopting microservices architecture. 

### Quick reminder about Microservices architecture 
Microservices architecture addresses all of the liabilities that are inherent in monolithic applications. 
microservices architecture allows

1. Different parts of our application to evolve on different timelines, 
2. They can be deployed separately, 
3. You choose your technology stack for each Microservice as it best fits the purpose, 
4. You can scale your services dynamically at runtime. Or let's say you can create individual instances of each individual service. 

But the most obvious advantage here is, if any part of the application fails, the whole application will not necessarily become unavailable/unresponsive to the customer, because they are not designed and operated as a single entity like in monolithic architecture.

### Microservices and Kubernetes

![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide67.png)
In the previous labs, we broke down our application into several microservices and then containerised them with Docker and let docker run them. So we converted our application into a multi-container application in order to remove that single point of failure. But here 's the problem: Docker is running on a single host. 

### Moving from Docker to Kubernetes
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide69.png)

And here we discuss why we need a containers orchestration platform like 
Kubernetes when moving from development to production. 

a multi-container application must run on a multi-host environment in order to 
eliminate that single point of failure. If one host went down our orchestration tool 
can switch the load to another host. 
We need to be able to create new instances of our individual microservices 
containers to scale accordingly.
When one or more of our services need to be updated, or let's say we are adding a 
new service to our mix, the orchestration platform must be able to automatically 
schedule new deployments and create new instances of our containers with zero 
downtime. 
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide73.png)

Kubernetes scales and manages our containers according to the available 
underlying resources on the host. Docker has a good view of what's happening to 
our containers, but not our host machine.  
Last but not least, Kubernetes checks our container continually to make sure 
they're healthy, and in case of any failure, it'll take actions to reinstate our 
deployment, create new instances or restore the services.

### Understanding Deployment Scenario in Kubernetes
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide70.png)

Now let's take a look at a deployment scenario on a high level, how we are going to deploy our application onto Kubernetes.

We broke down our application, built docker containers, deploying each docker container will spin up a pod with its docker container in there. Based on our deployment scenario, and the load, each pod gets replicated (and that way we're making new instances of the docker containers) -these pods are inside a worker, which we are showing them for simplicity. so we first created a deployment, and then  scale our deployment accordingly. Next step is to create a service, which allows our applications communicate with each within the cluster and also exposes our application to the internet and external networks. If the service type is a load balancer, Traffic coming to our application will be directed to the pods accordingly through the load-balancer service.  

### Kubernetes Concepts/Resources:

**Pod**:Group of one or more containers with shared storage/network and a specification for how to run the containers in a shared context.
**Deployment**:A set of multiple, identical Pods with no unique identities. It runs multiple replicas of your application, and automatically replaces any failed instances. 
**Node**:A virtual or a physical machine with multiple pods, where Master node automatically handles scheduling the pods across the Worker nodes in the cluster. 
**Service**:An abstraction which defines a logical set of Pods and a policy by which to access them. Service enables external access to a set of Pods. 
**Label**:Labels are key/value pairs that are attached to objects, such as pods. 
**Namespace**:Logical isolation/partitioning of resources in kubernetes cluster. 
  
Now that we know the key components, let's revisit our deployment scenario, this 
time in more details to see what's happening under the hood.
### Deployment under the hood
Firstly, we'll use KUBECTL CLI tool to interact with Kubernetes cluster. The kubectl 
lets you control Kubernetes clusters and its resources. 
Think of kubectl as your magic keyword to instruct Kubernetes from your terminal. 
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide72.png)
  
### Kubernetes Features: 

- Automated rollouts and rollbacks
- Automatic scaling and load balancing
- Self-healing
- Service discovery 
- Storage orchestration

Automated rolling out changes to a deployment and the ability to pause, resume 
and rollback to previous version if needed. 
Automatic scaling and load balancing: When traffic to a container spikes, 
Kubernetes can employ load balancing and scaling to distribute it across the 
network to maintain stability. 
Self-healing: When a container fails, Kubernetes can restart or replace it 
automatically; it can also take down containers that don't meet your health-check 
requirements. 
Service discovery: Kubernetes can automatically expose a container to the internet 
or to other containers using a DNS name and IP address.  
And finally, provisioning local or cloud storage for your containers as needed. 

### Prerequisites:
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide76.png)
In this part we are going to use minikube to spin up a single-node kubernetes cluster locally.
Here's the link to minikube on your machine:
```
https://kubernetes.io/docs/tasks/tools/install-minikube/
```
### What is minikube?
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide77.png)

#### Spin up a Kubernetes cluster
```
minikube start 
```
#### Start minikube by limiting the resources' utilization
```
minikube start --memory=8192 --cpus=3 --kubernetes-version=v1.17.4 --vm-driver=virtualbox 
```
#### Get cluster information
```
kubectl cluster-info  
```
#### Get cluster configuration
```
kubectl config view 
```
### Useful commands thorugh this section:

#### Get the list of Pods
```
kubectl get pods 
```
#### Get the list of Deployments
```
kubectl get deployment  
```
#### Pause minikube
```
kubectl pause minikube 
```
#### Stop minikube
```
kubectl stop minikube  
```
#### Starting Kubernetes dashbaord
```
kubectl minikube dashboard
```

### set minikube docker daemon 
```
eval $(minikube docker-env)   
```
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide80.png)
#### Verify you're using minikube's docker by looking up the images
```
docker get images
```
### Useful Commands for Docker

#### Getting the list of containers
```
docker container List  
```
#### Getting running docker containers
```
docker ps  
```
### Deploying an Application

#### Creating deployment with an image
```
kubectl create deployment [label] --image= [Image Name]
```
#### Getting details on deployment
```
kubectl describe deployment/[deployment] 
```
#### Getting logs for deployment 
```
kubectl get events 
```
### Scaling Applications

#### creating instances of the application by setting the replicas

![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide81.png)

#### Creating replicas and the processes under the hood

![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide82.png)

#### Scale deployment and setting replicas

```
kubectl scale deployment [Deployment Name] --replicas=4
```
#### Enabling application to automatically scale 

```
kubectl autoscale deployment [deployment] --min=1 --max=8 --cpu-percent=80  
```
#### Getting Info on Horizontal Pod Autoscaler 
```
kubectl get hpa
```
### Exposing an application
```
kubectl expose deployment [deployment Name] [--port=8082 ]  --type=NodePort
```
#### Getting list of services
```
kubectl get services
```
#### Pinging the application
```
curl [Master IP]:[NodePort]/hello/ 
```
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide84.png)
#### ssh into kubernetes cluster to ping the pod from within the cluster
```
minikube ssh  
```
#### Ping the container 
```
curl [Pod IP]:[container port]/hello/  
```
### Different types of Services for exposing applications

**ClusterIP**: This default type exposes the service on a cluster-internal IP. You can reach the service only from within the cluster. 

**NodePort**: This type of service exposes the service on each node’s IP at a static port. A ClusterIP service is created automatically, and the NodePort service will route to it. From outside the cluster, you can contact the NodePort service by using “<NodeIP>:<NodePort>”.

**LoadBalancer**: This service type exposes the service externally using the load balancer of your cloud provider. The external load balancer routes to your NodePort and ClusterIP services, which are created automatically

### Different types of ports for accessing application from within the cluster, from outside the node and form outside the cluster.

**NodePort**: This setting makes the service visible outside the Kubernetes cluster by the node’s IP address and the port number declared in this property. The service also has to be of type NodePort (if this field isn’t specified, Kubernetes will allocate a node port automatically).

**Port**: Expose the service on the specified port internally within the cluster. That is, the service becomes visible on this port, and will send requests made to this port to the pods selected by the service.

**TargetPort**: This is the port on the pod that the request gets sent to. Your application needs to be listening for network requests on this port for the service to work.

![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide86.png)

### Exposing application with type LoadBalancer
```
kubectl expose deployment [deployment Name] [--port=8082 ] --type=LoadBalancer
```
#### Getting the Cluster-IP for the Kubernetes Cluster
```
kubectl cluster-info  
```
#### This command doesn't work as Minikube doesn't allocate the external IP address
```
curl [LoadBalancer External IP]:[Node Port]/hello/ 
```
(minikube is a single node cluster. therefore its IP address is the same node IP)
#### Pinging the container using minikube cluster IP instead worker node IP and NodePort
```
curl [kubernetes Cluster-IP]:[Node Port]/hello/ 
```
#### Now let's try to access the pod from within the cluster
```
minikube ssh 
```
#### Using the Load Balancer IP and container Port
```
curl [LoadBalancer Cluster IP(internal)]:[Port]/hello/ 
```

### Rolling out updates

![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide91.png)

Rolling updates allow Deployments' update to take place with zero downtime by incrementally updating Pods instances with new ones. Performing updates without affecting application availability.

![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide139.png)

In this part we're going to update our image to the parser for covid-19 mortality data, which reflects the number of death in every country country and region.  
```
kubectl set image deployment/[deployment name]  [container]=[new image]
```

Make sure you use the container name in the above command to update the image in it. 
To get the container name, use: 
```
kubectl get deployment -o wide
```
verify the deployment is updated by pinging the app
```
curl ip:port/hello/
curl ip:port/get/country/data/germany/
```

To rollback to the previous version use:
```
kubectl rollout undo deployment/[deployment Name] 
```
optional: You can add --to-revision=n in order to rollback to a specific verison
```
kubectl rollout undo deployment/[deployment Name] --to-revision=2
```
checkout the rollout status 
```
kubectl rollout status deployment/[deployment Name]
```
### What is YAML?
YAML is a human-readable, data serialization standard for specifying configuration-type information. 
YAML can be used for common use cases such as:

- Configuration files
- Inter-process messaging
- Cross-language data sharing

![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide95.png)

Kubernetes resources are represented as objects and can be expressed in **YAML** or **JSON** format
Examples: 
Print deployment as Yaml
```
kubectl get deployment –o yaml [json]
```
Print services as Yaml
```
kubectl get services –o yaml
```
## Using YAML to create resources 
  
A sample YAML file to create a ***Pod*** with a specific container from the specified image  (i.e. my-app:v2) listening on Port 80:  
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide97.png)  
A sample YAML file to create a ***Deployment*** with 2 replicas:  
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide98.png)  
A sample YAML to create a NodePort ***Service*** and letting Kubernetes asign the ports automatically by leaving the ports config blank  
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide99.png)  
A sample YAML to create ***Replicaset*** with 3 Replicas  
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide100.png)  
A sample YAML to update the ***Replicaset*** to 6 Replicas
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide101.png)  

### Once the YAML file is crafted, here is how to apply it:
```
kubectl apply -f [fileName].yaml
```
#### Get logs of applying YAML file
```
kubectl log –l app=[container name]
```
Here the summary of what we have learnt in this section:  
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide103.png)

