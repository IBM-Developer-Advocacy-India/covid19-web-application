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
