# Kubernetes Deployment in AWS

**Author:** Gilbert Emodi  
**Email:** zemodi99@gmail.com

---

![Image](https://github.com/GilbertEmodi/Launch-Kubernetes-Cluster-part1-/blob/main/eks1-architecture-complete.png)

---

### What is Amazon EKS?

In this first section of the project, I will deploy a Kubernetes cluster using Amazon EKS. EKS is AWS' service for deploying Kubernetes in the cloud. I will also be using exploring "eksctl" and CloudFormation

Amazon EKS is AWS' cloud Kubernetes service, which means it simplifies managing and orchestration of Kubernetes clusters. I used it in today's project to create a Kubernetes cluster.

### One thing I didn't expect

One thing I didn't expect in this project was seeing how eksctl wasn't already installed on the EC2 instance, but using it was well worth it. It simplified resource creation in comparison to using just the AWS CLI commands.

---

## What is Kubernetes?

Kubernetes is a tool for container orchestration. Companies and developers use Kubernetes to deploy and manage large scale containerized applications.

I used "eksctl" to create a Kubernetes cluster using the command line. The "eksctl create cluster" command line I ran defined the name of the cluster, its node group's name, and node size settings. I also defined the region and the EC2 instance type.

I initially ran into 2 errors while using eksctl. The first one was because of not having installed eksctl yet.
The second one was because the EC2 instance didn't have permission to our AWS account and services yet. I solved that issue by assigning an IAM role to the EC2 Instance.

![Image](https://github.com/GilbertEmodi/Launch-Kubernetes-Cluster-part1-/blob/main/1-eksctl%20not%20found.JPG)

---

## eksctl and CloudFormation

CloudFormation helped create my EKS cluster because "eksctl" uses Cloud Formation under the hood when I run an "eksctl create" command. CloudFormation created VPC resources because creating the EKS cluster in my default VPC would cause compatibility and permission issues.

There was also a 2nd CloudFormation stack for the node group. The difference between a cluster and node group is that the cluster is the entire Kubernetes set up (including the control plane), while the node group is a group of EC2 instances inside.

![Image](https://github.com/GilbertEmodi/Launch-Kubernetes-Cluster-part1-/blob/main/2-Stacks%20Created.JPG)

---

## The EKS console

I had to create an IAM access entry in order to see the nodes in the new node group. An access entry is a mapping of AWS IAM policies to Kubernetes' access control system. I set it up by using the Access Entries page within the EKS management console

It took about 40 minutes to create my cluster. Since I'll create this cluster again in the next part of the project, I can speed up the process by using templates (ex. Terraform or CloudFormation) and installing dependencies ahead of time.

![Image](https://github.com/GilbertEmodi/Launch-Kubernetes-Cluster-part1-/blob/main/3-Nodes.JPG)

---
# Set Up Kubernetes Deployment
---

![Image](https://github.com/GilbertEmodi/Set-Up-Kubernetes-Deployment-part2-/blob/main/eks2-architecture-complete.png)

---

## Part 2

In this Section, I will prepare a backend app for Kubernetes deployment. This is because Kubernetes deployment requires apps to be containerized and for there to be a container image. This part of the project will get us to create that container image.

### Tools and concepts

'I used Amazon EKS, Git, Docker, and Amazon ECR to prepare a backend app for deployment with Kubernetes. This involved setting up an EKS cluster, cloning code from GitHub, building and pushing a docker image to Amazon ECR.

Something new that I learnt from this experience was how to properly set up the AWS CLI to run linux commands that help streamline my Kubernetes cluster deployment.

---

## What I'm deploying

To set up this section, I launched a Kubernetes cluster. Steps I took to do this included launching an EC2 instance, installing "eksctl" by running the command, and modifying the IAM Role for my instance so that it has "AdministratorAccess". 

### I'm deploying an app's backend

Next, I retrieved the backend that I plan to deploy. An app's backend means the logic/brain that defines how the app "works". I retrieved backend code by cloning it from a GitHub repository.

![Image](https://github.com/GilbertEmodi/Set-Up-Kubernetes-Deployment-part2-/blob/main/1-Backend%20Cloned.JPG)

---

## Building a container image

Once I cloned the backend code, my next step is to build a container image of the backend. This is because Kubernetes needs a container image for a successful app deployment. At the moment I haven't prepared a container image yet, (just raw code).

When I tried to build a Docker image of the backend, I ran into a permissions error because I am logged into the EC2 instance as "ec2-user" (created automatically from the EC2's AMI). "ec2-user" can not run root-user level commands without "sudo"

To solve the permissions error, I added the ec2-user to the Docker group. The Docker group is a group in Linux systems that grant a user the permission to run docker commands, (like docker build).

![Image](https://github.com/GilbertEmodi/Set-Up-Kubernetes-Deployment-part2-/blob/main/2-Docker%20Image%20Building.JPG)

---

## Container Registry

I'm using Amazon ECR in this project to store our container image. ECR is a good choice for the job because it is an AWS service and integrates well with other AWS services like EKS. This makes deploying the app even faster for engineers & devs.

Container registries like Amazon ECR are great for Kubernetes deployment because I get to store tagged images from a single source of truth. Users and other services can pull my latest container image without any manual downloading required.

![Image](https://github.com/GilbertEmodi/Set-Up-Kubernetes-Deployment-part2-/blob/main/3-ECR%20Repository.JPG)

---

## Backend Explained

After reviewing the app's backend code, I've learned that the app functions by extracting data from another API, but we also have our own API in "app.py" that's responsible for others wanting access to our service.

### Unpacking three key backend files

The requirements.txt file lists all the dependencies and libraries that every container needs to install when the container is created.

The Dockerfile sets up the instructions that tell Docker how it should build a container image of your backend app. It includes commands on where it can find a list of all dependencies to install (requirements.txt) and commands that automatically run

The app.py file contains three main parts: installing dependencies, formatting the data into JSON data, passing the formatted data back to the user/requester.

---
# Create Kubernetes Manifests

![Image](https://github.com/GilbertEmodi/Create-Kubernetes-Manifests-pt.3-/blob/main/Kubernetes%203.png)

---

In this section, I will set up manifest files because they are a key part of the Kubernetes deployment process. Manifest files give Kubernetes the instructions on HOW it should deploy the containerized application.

### Tools and concepts

I used Amazon EKS, eksctl, Git, Docker, ECR to create a Kubernetes cluster and build the container image of the backend of an app to deploy. Key concepts include using Manifest files, Deployments and Services to get ready for a Kubernetes deployment.

My favourite part was also being able to set up manifests files and understand how they work together.

---

## Set-Up (Previous Steps)

In the previous sections, I launched a Kubernetes cluster. Steps I took to do this included granting the EC2 instance permissions with IAM, and installing the eksctl command line tool. I created the cluster using the "eksctl" create command.

### Backend code

I retrieved the backend that I plan to deploy by cloning it from my team member's GitHub repository. Backend is the "brain" or logic of an application. For example, how an app stores, retrieves, and processes data.

### Container image

Once I cloned the backend code, I built a container image because Kubernetes requires apps to be in container images to deploy them. I used Docker to build a container image of the backend.

I also pushed the container image to a container registry, because this helps establish a single source of truth for the latest version of the container. To push the image to ECR, I ran the push commands that built and tagged the image.

---

## Manifest files (Part 3)

Kubernetes manifests are instructions for Kubernetes on how it should deploy the app. Manifests are helpful because they reduce manual work when we're wanting to deploy and manage applications across multiple containers (clusters).

A Kubernetes deployment manages multiple copies of the same containerized backened. The container image URL in my Deployment manifest tells Kubernetes the attributes of the container that I'd like Kuernetes to deploy.

![Image](https://github.com/GilbertEmodi/Create-Kubernetes-Manifests-pt.3-/blob/main/1-manifests.png)

---

## Service Manifest

A Kubernetes Service exposes the application to network traffic. You need a Service manifest because the deployment manifest takes care of creating/managing the application except how users/apps will get access to them. No service manifest=no traffic

My Service manifest sets up a NodePort and targetPort, which means it maps traffic that get to port 8080 of the service to port 8080 of a container running the application.

![Image](https://github.com/GilbertEmodi/Create-Kubernetes-Manifests-pt.3-/blob/main/2-ServiceManifests.png)

---

## Deployment Manifest

Annotating YAML files helped me understand the ins and outs of each command in the deployment manifest because I broke down and explained each line & their relationship to other commands too.

A notable line in the Deployment manifest is the number of replicas, which means the number of copies of the app I'd like to deploy across the cluster. Pods are relevant to this because 3 replicas = 3 pods (1:1 relationship).

One part of the Deployment manifest I still want to know more about is the namespace of a deployment because I am not too clear what it means and how it impacts Deployment/how it's used as metadata.

![Image](https://github.com/GilbertEmodi/Create-Kubernetes-Manifests-pt.3-/blob/main/3-AnnotatedManifestFinal.png)

---

# Deploy Backend with Kubernetes

---

## Deploy Backend with Kubernetes

![Image](https://github.com/GilbertEmodi/Kubernetes/blob/main/architecture-complete%20kubernetes%20pt%204.png)

---

In this final section of the project, I will finally deploy the backend of an application with Kubernetes.

---

## Manifest files

Kubernetes Manifests are the instruction manuals on how I'd like Kubernetes to to deploy, expose, and run the containerized application. Manifests are helpful because it allows Kubernetes to repeat a deployment using the same instructions.

A Deployment manifest manages the creation of a Deployment resource that's responsible for creating containers/rolling out updates across the EKS cluster. The container image URL in my deployment manifest tells Kubernetes where my image is.

A Service exposes a deployed application to users/traffic. My service manifest sets up a Service resource that exposes the backend application at port 8080 within the EKS cluster. 

![Image](http://learn.nextwork.org/ecstatic_beige_calm_tarapirohe/uploads/aws-compute-eks4_b01876554)

---

## Backend Deployment!

To deploy my backend application, I applied the manifest files that I created. Applying a manifest file means giving Kubernetes the templates for creating the Deployment and Services Resources to deploy & expose the application

### kubectl

"kubectl" is the command line tool for managing resources inside the cluster. I need this tool to deploy the application by creating the Deployment/Service resources. I can't use "eksctl" for the job because that tool is for creating the cluster.

![Image](http://learn.nextwork.org/ecstatic_beige_calm_tarapirohe/uploads/aws-compute-eks4_6cfb382f2)

---

## Verifying Deployment

The final step is to use the EKS console to verify the deployment. I had to set up IAM access policies because I didn't have access to see the cluster's nodes by default. I set up access by running the "iamidentitymapping" command.

Once I accessed my cluster's nodes, I discovered pods running inside each node. Pods are the smallest deployable unit and represent bundles of containers. Containers in a pod share networking and storage resources, making the app run efficiently.

The EKS console shows the events for each pod, where I could see the container image getting pulled and getting used to create a new container running the backend application. This validated that the deployment was a success, the backend is runnining

![Image](http://learn.nextwork.org/ecstatic_beige_calm_tarapirohe/uploads/aws-compute-eks4_3b391f873)

---

---

