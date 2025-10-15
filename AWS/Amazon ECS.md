#aws  #cloud 
# Prerequisite
[[Container]]
# Overview
+ Elastic Container Service
+ __Fully managed__ container orchestration service for Docker containers.
	+ Manage, create and scale containerized applications.
+ Amazon ECR => [[Docker Image#Container Registry|container registry service]].
# Architecture
There are 3 layers :
1. Capacity:  Infrastructure where containers are deployed.
2. Controller: Manages and deploys containers
3. Provisioning: Tools to interface with Controller to manage and deploy containers.
![Diagram showing the capacity, controller, and provisioning layers.](https://docs.aws.amazon.com/images/AmazonECS/latest/developerguide/images/ecs-layers.png)
# Capacity
A capacity strategy can contain only one of the following:
## [[Amazon Elastic Compute Cloud (AWS EC2)#EC2 Instance| EC2 Instances]]
+ Need to manage, deploy and maintain EC2 instances yourself
+ Each EC2 instance runs the ECS agent to register in the ECS cluster. 
	+ ECS then deploys containers on it as per requirements.
+ Can scale using [[Amazon Elastic Compute Cloud (AWS EC2)#Auto Scaling Group (ASG)|Auto-scaling groups]].
+ Ideal for predictable workloads.
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_31788258.png]]
## Fargate 
+  Fargate manages its own infrastructure i.e. handles deployment, scaling, maintenance.
+ Launch Docker containers based on task definition.
	+ A ECS task can be a single container or a group of containers.
+ ECS tasks are auto-provisioned based on task definition.
	+ Can define CPU, memory, networking, IAM policies etc..
+ Each task runs in its own dedicated, isolated runtime environment which enhances security.
+ Ideal for dynamic or unpredictable workloads
# IAM Roles
## EC2 Instance Profile
+ Only for EC2 instance launch type.
+ IAM role is attached to EC2 Instance Profile which in turn is attached to a EC2 instance.
+ Allows secure access of IAM role credentials (See [[Amazon Elastic Compute Cloud (AWS EC2)#Instance Metadata Service (IMDS)|IMDS]] for more info).
+ Used by ECS agent to:
	+ Make API calls to ECS
	+ Send container logs to CloudWatch
	+ Pull Docker image from ECR
## ECS Task Role
+ Can be used by EC2 instance launch type or Fargate launch type.
+ Allows each task to have a specific role, unlike [[#EC2 Instance Profile]] where all tasks in same instance share permissions.
	+ IAM role with a [[IAM#Trust Policies|trust policy]] that allows `ecs-tasks.amazonaws.com` service to assume the role.
+ It is specified in the task definition.
+ Allows secure access of IAM role credentials.
# Load Balancer Integrations