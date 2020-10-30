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
> you need to set your environmental parameter to use minikube docker. This command will let you do that:
```
eval $(minikube docker-env) 
```
This step is not needed here, is intended to let you know what we will use minikube's docker.
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide55.png)

## Part 3: Deploy, Run and Maange your Docker Containers with Kubernetes.  
  
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
docker images
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

## Part 4: Build, Deploy and Manage your Microservices Application with OpenShift.
     
  
### Agenda
In this section you will learn:
- Why OpenShift?
- Kubernetes vs. OpenShift
- Developer productivity
- Deploy on OpenShift via CLI
  - Pushing image to registry
  - Create deployment
  - Expose 
- Deploy on OpenShift via Console
  - OpenShift Console
  - Builder Images
  - S2I (Source to Image)
--- 

![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide108.png)

### What is OpenShift Container Platform?
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide114.png)
OpenShift is built on top of Kubernetes, and brings 
along all the brilliant features of Kubernetes, but it bundles Kubernetes with all the Essential features that will ultimately provide the best experience to both developers and 
Operation engineers. 
But how does it achieve that?
Through a number of automated workflows, which are not available in Kubernetes. 
Those automated workflows are the results of these components that are drawn in this diagram. 
Kubernetes is wrapped around an enterprise-grade linux operating system (RHEL/CoreOS), Networking, monitoring, registry, 
And more importantly, authentication and authorisation. 

### 3 x key features of OpenShift over Kubernetes. Automation, Agility and Security. 

![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide115.png)

### what are the automated workflows?

![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide116.png)

- As a developer you want to get started on coding as quickly as possible, rather than 
spending time learning about different platforms, tools and services, and how to 
refactor your application based on them. 

Pre-created quick start application templates to build your application, based on your favourite languages, frameworks, and databases, with one click.

- As a developer you want to focus on coding and not worrying about what's going to happen in the background.

Deploying to OpenShift is as easy as clicking a button or entering a **git push** command, **enabling continuous integration**, managing builds, and allows you to fully control the deployment lifecycle.

- As a developer you want to build and test your application locally, without worrying about the openshift cluster your application will end up running in.

Develop container-based applications in the cloud or locally using the Red Hat CodeReady Containers to create a fully-functioning OpenShift instance **on your local machine**. Then, deploy your work to any OpenShift cluster.

![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide117.png)

As this figure shows developers can focus on coding, and the rest of the process is taken care of by OpenShift's S2I or Source to Image. Building your image, deploying, and as you will later in part 7, continues integration. 

### Three major differences between Kubernetes and OpenShift

#### CLI vs. Console
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide118.png)

One of the most distinctive features of OpenShift is its amazing web console that allows to implements almost all tasks from a simple graphical interface. As you saw in the previous lab, Kubernetes dashboard is only good for displaying the status of your resources. You can't deploy, control or manage your applications, networking or any of those form Kubernetes dashboard.  Obviously, managed Kubernetes on different cloud platforms, come with different set of functionalities as add-ons.  But with Openshift container platfomr, the offered functionalities through the openshift console are vast. You can build, deploy, expose, update, and almost implement any task in two separate perspectives of developer and administrator. We'll go through that later in this lab.  

#### Project vs. Product
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide119.png)

Kubernetes is an opensource project, where as Openshift is a product based on an open source project, which is Kubernetes Origin Distribution or OKD. [next] Comparing Kubernetes with OpenShift is like that classical example of comparing an engine with a car. You can't do much with an engine, and you need to assemble it with other components in order to get from A to B and become productive. What you get with OpenShift includes enterprise support, ecosystem certification And most importantly, regular releases and security updates at every level of the container stack and throughout the application lifecycle. That is an opinionated integration of features to simplify and secure your applications.

#### Cloud Platforms Offerings
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide121.png)

Kubernetes offerings differ from one platform to another. Almost every major cloud provider offers a different flavour of Kubernetes. You get different sets of add-ons, plug-in and set of instructions for connecting your application to your cloud resources, which in most cases are only applicable to that particular platform. With openshift container platform, your experience and the way you interact with with the platform, let's say the openshift console, stays the same. Therefore, building, deploying and managing applications with Openshift container platform is truly: build it once and deploy it anywhere. 

![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide122.png)

In this lab we're going to use managed openshift on IBM Cloud. Before continuing, 
let's get started by provisions an OpenShift cluster on IBM Cloud.  
Red Hat® OpenShift on IBM Cloud™ is a fully managed OpenShift service that leverages the enterprise scale and security of IBM Cloud, so you can focus on growing applications, not scaling the master. 
IBM has added unique security and productivity capabilities designed to eliminate substantial time spent on updating, scaling and provisioning.

![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide123.png)

Once you've signed up on IBM Cloud and sign into your account by visiting cloud.ibm.com, you need to navigate through ibm cloud dashboard and choose OpenShift. Then go ahead and create your cluster. Once your cluster is provisioned and ready, it'll be listed in this table. 

### Download and Install prerequisites

Install IBM CLI tools
```
curl -sL https://ibm.biz/idt-installer | bash
```
Download OC CLI based on local OS and OpenShift version
```
https://mirror.openshift.com/pub/openshift-v4/clients/oc/
```
Download kubectl
```
https://storage.googleapis.com/Kubernetesrelease/release/v1.17.7/bin/darwin/amd64/kubectl
```
Set your environmental parameters for OC
```
mv /<filepath>/oc /usr/local/bin/oc
```
Set your environmental parameters for kubectl
```
mv /<filepath>/kubectl/usr/local/bin/kubectl
```
### Login to IBM Cloud and check your installed plugins

Login to IBM Cloud
```
ibmcloud login
```
if using a federated account
```
ibmcloud login --sso
```
List IBM Cloud plugins
```
ibmcloud plugin list
```
List IBM Cloud Openshift clusters
```
ibmcloud oc cluster ls
```
Initialize OC CLI Client
```
ibmcloud oc init
```
Log your local Docker daemon into the IBM Cloud Container Registry
```
ibmcloud cr login
```
Test your OC CLI
```
ibmcloud oc
```
Test your Container Registry
```
ibmcloud cr 
```
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide126.png)

### Push Image to IBM Container Registry

Create a new namespace in IBM Cloud Container Registry
```
ibmcloud cr namespace-add [namespace]
```

Tag the image
```
docker tag [image name] us.icr.io/[namespace]/[image name]
```

Push the image to container registry
```
docker push us.icr.io/[namespace]/[image name]
```

List images in IBM Cloud Continer Registry
```
ibmcloud cr image-list 
```

### OC commands
The developer OC CLI allows interaction with the various objects that are managed by OpenShift Container Platform. 

Here is the format of OC commands, almost identical with Kubectl
```
oc <action> <object_type> <object_name>
```
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide128.png)

View existing projects
```
oc projects 
```

Switch to a project
```
oc project [project name]
```

Create a new project
```
oc new-project [name project]
```

### Some useful OC commands

Get the full list of OC commands and parameters
```
oc --help
```

In-depth look into the values to be set
```
oc explain [resource]
```

Edit the desired object type
```
oc edit <object_type>/<object_name>
```

Updates one or more fields of an object (The changes is a JSON or YAML expression containing the new fields and the values)
```
oc patch <object_type> <object_name> -p <changes>
```
### Create Deployment using an image from IBM Cloud Container Registry

Create a deployment by instructing the OpenShift cluster to pull an image from ICR
```
oc create deployment [dep name] --image=us.icr.io/covid-test/myapp:v1
```
   
Get the list of deployments (same as Kubectl)
```
oc get deployment
```  

Get the list of pods (same as Kubectl)
```
oc get pods
``` 

### Expose the current deployment to the Internet

Expose the deployment on container port 8082 with LoadBalancer service type
```
oc expose deployment/mytestservice --port=8082 --type=LoadBalancer
```

Get the list of services
```
 oc get services
```
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide131.png)

Every OpenShift project has a Kubernetes service account that is named **default**. Within the project, you can add the image pull secret to this service account to grant access for pods to pull images from your registry. 

### Pull Images from ICR into non-Default Projects

- Create an IBM Cloud IAM service ID for your cluster that is used for the IAM policies and API key credentials in the image pull secret. 
- Create a custom IBM Cloud IAM policy for your cluster service ID that grants access to IBM Cloud Container Registry.
- Create an API key for the service ID
- Create an image pull secret to store the API key credentials in the cluster project
- Store the registry credentials in a Kubernetes image pull secret and reference this secret from your configuration file.
- Add the image pull secret to your default service account.

Create an IBM Cloud IAM service ID 
```
ibmcloud iam service-id-create cluster-project-id --description "service ID for cluster-project"
```
Create a custom IBM Cloud IAM policy for your cluster service ID 
```
ibmcloud iam service-policy-create iam-service-id --roles Manager --service-name container-registry 
```
Create an API key for the service ID 
```
ibmcloud iam service-api-key-create [api-key-name] [service-policy-id] --description "API Key"
```
Create an image pull secret to store the API key & store the registry credentials in K8s image pull secret
```
oc --namespace [project] create secret docker-registry [secret name] --docker-server=us.icr.io --docker-username=iamapikey --docker-password=[API-key] --docker-email=[]
```
Get all secrets in project 
```
oc get secrets --namespace [project]
```

Get secrets in 'default' serviceaccount in project [] 
```
oc describe serviceaccount default -n [project]
```
Add the image pull secret to your default service account
```
oc patch -n <project_name> serviceaccount/default --type='json' -p='[{"op":"add","path":"/imagePullSecrets/-","value":{"name":"<image_pull_secret_name>"}}]'
```

Check the secrets again to verify the secret has been added the default serviceaccoun. 

Get secrets in 'default' serviceaccount in project [] 
```
oc describe serviceaccount default -n [project]
```
### Verify that the new project can pull images from ICR

Create a deployment by pulling an image from ICR into the new peoject
```
oc create deployment [new project] --image=us.icr.io/covid-test/myapp:v1
```

verify that image has been pulled and deployed successfully
```
oc get deployment
```
Expose the deployment
```
oc expose deployment/mytestservice --port=8082 --type=LoadBalancer
```
Verify the service is up and running
```
oc get services
```
### Scale and Replicas

in this section we will create replicas of our deployed application. Openshift will considers the instructed number of instances as the desired state. If any pod fails or destroyed, OpenShift will bring that back up to keep the number of instances intact in order to meet the load.

![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide136.png)

Sclae the application by creating 3 more instances 
```
oc scale --replicas=4 deployment/[deployed resource]
```
Get the replicas
```
oc get rs
```
Verify the number of running pods (reflecting the number of instances)
```
oc get pods –o wide
```
### Rolling out updates and Rolling back
![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide138.png)

Rolling updates allow Deployments' update to take place with zero downtime by incrementally updating Pods instances with new ones. Performing updates without affecting application availability.

![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide139.png)

In this part we're going to update our image to the parser for covid-19 mortality data reflect the number of death in every country country and region.  
```
oc set image deployment/[deployment name]  [container]=[new image]
```

Make sure you use the container name in the above command to update the image in it. 
To get the container name, use: 
```
oc get deployment -o wide
```
verify the deployment is updated by pinging the app
```
curl ip:port/hello/
curl ip:port/get/country/data/germany/
```

To rollback to the previous version use:
```
oc rollout undo deployment/[deployment Name] 
```
optional: You can add --to-revision=n in order to rollback to a specific verison
```
oc rollout undo deployment/[deployment Name] --to-revision=2
```
checkout the rollout status 
```
oc rollout status deployment/[deployment Name]
```

![alt text](https://github.com/mohaghighi/Covid19-Web-Application/raw/master/images/Labs/Slide141.png)
 
--- 

