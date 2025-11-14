#aws  #cloud 
```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
include: 
exclude: 
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
# Prerequisite
[[Container]]
# Overview
+ Elastic Container Service
+ __Fully managed__ container orchestration service for Docker containers.
	+ Manage, create and scale containerized applications.
+ Amazon ECR => [[Docker Image#Container Registry|container registry service]].
+ Region-bound service, available only in enabled AZ's.
# Architecture
There are 3 layers :
1. Capacity:  Infrastructure where containers are deployed.
2. Controller: Manages and deploys containers
3. Provisioning: Tools to interface with Controller to manage and deploy containers.
![Diagram showing the capacity, controller, and provisioning layers.](https://docs.aws.amazon.com/images/AmazonECS/latest/developerguide/images/ecs-layers.png)
# Terminology
## ECS Task
+ Smallest, independently run unit in a cluster.
+ Instance of a __task definition__ which provides a blueprint for how a container/group of containers should run.
## ECS Service
+ Ensures a _desired amount_ of tasks are running at any given time, replacing any failed tasks automatically.
+ Helps integrate with load balancer by automatically registering/deregistering tasks to target group.
+ Scales tasks up/down based on demand/metric.
	+ [[Amazon Elastic Compute Cloud (AWS EC2)#^04dd8f|Target Tracking]]
	+ [[Amazon Elastic Compute Cloud (AWS EC2)#^447dac|Step Scaling]]
	+ [[Amazon Elastic Compute Cloud (AWS EC2)#^55db8e|Scheduled Scaling]]

## ECS Cluster
+ A logical grouping of tasks or services that provide the infrastructure capacity for containers.
	+ Can be a mix of different capacity compute types.
+ Manages shared pool of resources that tasks and services use.
	+ Network (VPC + Subnet)
	+ Monitoring tools
# Capacity Compute Types
## [[Amazon Elastic Compute Cloud (AWS EC2)#EC2 Instance| EC2 Instances]]

^582b21

+ Need to manage, deploy and maintain EC2 instances yourself
+ Each EC2 instance runs the ECS agent to register in the ECS cluster. 
	+ ECS then deploys containers on it as per requirements.(See [[#ECS Task Placement]] for more details)
+ Can scale using:
	+ [[Amazon Elastic Compute Cloud (AWS EC2)#Auto Scaling Group (ASG)|Auto-scaling groups]].
	+ __ECS Cluster Capacity Provider__ (recommended): auto-provision and scale EC2 instances used for ECS tasks i.e. you don't have to manage EC2 instances yourself.
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
# Task Definition
+ JSON file that provides a blueprint for an ECS task to the ECS service.
+ Can define up to 10 containers per task definition.
+ Contains information like:
	+ [[Docker Image|Image]] name
	+ Memory and CPU needed
	+ Networking info
	+ IAM role (one role per task definition)
	+ Logging config
	+ Environment variables
		+ Can be:
			+ Hardcoded (ex: URLs)
			+ in SSM Parameter Store/Secrets Manager (ex: API keys, DB passwords)
			+ stored as a env file in [[Amazon S3#General Purpose Buckets|S3 bucket]]. 
		+ Values injected in to ECS task
	+ Port Binding for Container and Host![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_31788346.png]]
	+ At least one container defined in the task definition must be a __essential container__. If an essential container fails, then the entire task is stopped i.e. all containers defined in the associated task definition are killed.
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
	+ Only one role can be specified per task definition, so for two tasks to have different roles, they must be derived from different task definitions.
	+ Difference is, unlike [[#EC2 Instance Profile]], such tasks can share the same host/underlying infrastructure.
+ Allows secure access of IAM role credentials.
# Load Balancer Integration
Read [[Amazon ELB]] before proceeding
## Bridge mode
+ Used with [[#^582b21|EC2 Instance Launch Type]], ALB, NLB
+ A virtual network bridge is created on the EC2 instance, which allows dynamic port mapping, where a ECS task is randomly associated with an available port on the EC2 instance (`containerport`), dynamically by the ECS agent.
+ Multiple tasks from same service use different ports and share the EC2 instance's private IP address.
>[!note]
>The security group of the EC2 instance __must allow__ inbound traffic from any port of the ELB for dynamic port mapping to work.
## Workflow
1. Set `hostPort` to 0 in task definition to enable dynamic port mapping for the ECS task.
2. Associate the task with a target group and load balancer. The target group is configured to send traffic to the ECS tasks.
	1. Target type can be `instance` (EC2 instance ID) or `ip` (EC2 instance private IP) .
3. When ECS scheduler creates a new task it is automatically registered with the target group __using instance id/ip and dynamic port that the task is mapped to__.
4. If unhealthy or destroyed, it is automatically deregistered from the target group.

>[!example]
> The ECS scheduler places Task A on your EC2 instance and assigns it a random available port, for example, `65123`. 
> It then places Task B on the same EC2 instance and assigns it a different random port, for example, `23751`.
> <br/>
> ECS registers `ec2-instance-ip:65123` for Task A and `ec2-instance-ip:23751` for Task B in the same target group.
> <br/>
> A user makes a request to your ALB's DNS name. The ALB receives the request and, based on its load-balancing algorithm (e.g., round-robin), forwards the request to either `ec2-instance-ip:65123` or `ec2-instance-ip:23751`.
> </br>
> The EC2 instance's network stack receives the request on the specific port assigned to the task. It then forwards the request to the correct container.![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_31788346 (1).png]]
## awsvpc Networking mode
+ Used with [[#Fargate]], ALB, NLB
+ Each task gets its own dedicated ENI(elastic network interface) and private IP address from associated [[Amazon VPC]] subnet.
+ The load balancer routes traffic directly to the task's private IP address. i.e target group type is `ip` .
+ Since each task has a private IP, multiple tasks from same service can use the same `containerport`.
## Workflow
1. Associate the task with a target group and load balancer. The target group is configured to send traffic to the ECS tasks.
	1. Target type is `ip` (ECS task private IP) .
2. When ECS scheduler creates a new task it is automatically registered with the target group __using instance id/ip and static port that the task is mapped to__.
3. If unhealthy or destroyed, it is automatically deregistered from the target group.
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_31788346 (2).png]]
>[!note]
>The ECS task's ENI security group must allow inbound traffic from the static port (here 80) of the ELB.
# CLB integration (Not recommended)
+ Since CLB supports only one port mapping, to integrate with CLB, we are limited to one ECS task per EC2 instance.
+ Cannot be used with Fargate.
# Data persistence
Options work for both EC2 instances and Fargate.
>[!note]
>S3 cannot be mounted as a file system
## [[Amazon EBS]]
+ Need to specify mount path and volume name, along with any attributes like volume type, IOPS and throughput, in the task definition.
+ ECS auto-provisions the volume and attaches it to a newly created task.
	+ By default, deleted when task stops. (Delete on termination option can be unchecked to persist it).
+ Can be accessed by only one task at a time.
+ Use for ETL jobs, ML inference and other workloads requiring high-speed local processing.
## [[Amazon EFS]]
+ Need to specify mount point and volume name in task definition.
+ ECS ensures EFS file system is mounted and available for access when new task is created.
+ Data persists by default, when a task stops or is destroyed.
+ Can be accessed by multiple tasks concurrently.
	+ Tasks can be on different hosts
+ Storage is elastic i.e. can grow and shrink as needed.
+ Use for shared storage (datasets, files, web serving).
## [[Persisting Data#Bind volumes|Bind mounts]]
+ Shared storage for multiple tasks on the same host.
+ For EC2 instances , uses [[Storage in AWS#^233511|EC2 Instance Storage]] and for Fargate uses ephemeral storage (default 20 GB).
+ Storage is tied to the host(EC2)/container(Fargate) lifecycle i.e. it is deleted when either the host is terminated or if all tasks referring to it are stopped or terminated.
# ECS Rolling Updates
When updating task definition from old version (say v1) to a newer version (say v2), rolling updates can be used to ensure that tasks are still available and running while the change happens in the background in phases.

To do this, we need to specify:
+ _Minimum healthy %_: % of ECS tasks that must be running at any given time. Between 0-100%.
+ _Maximum %_: Maximum no of tasks that can be running at any given time. 

> [!example]
> Min: 50%, Max: 100%![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_31788300.png]]
> <br/>
> Min: 100%, Max: 150%![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_31788300 (1).png]]

# ECS Task Placement
+ The scheduler determines:
	+ in which EC2 instance a task should be placed, given the constraints of memory, CPU and available ports.
	+ which task should be terminated during a scale in event.
+ The process is as follows:
	+ Identify EC2 instances in the ECS cluster that satisfy CPU, memory and port requirements stated in the task definition.
	+ Then identify the EC2 instances which satisfy task placement constraints
	+ Finally, identify the EC2 instances among them which satisfy the task placement strategy
	+ Select EC2 instance(s) for task placement.
	+ If none found, a new EC2 instance is created.
+ Both strategies and constraints are defined in ECS service.
## Constraints
### __distinctInstance__
+ Place each task on a separate instance
```json
{
"placementConstraints": [
		{
			"type": "distinctInstance"
		},
		...
	]
}
```
### __memberOf__
+ Places task on instances that satisfy a expression.
+ Expression defined using Cluster Query Language.
```json
{
// Can place task only in instance of type t2.*
"placementConstraints": [
		{
			"expression" : "attribute:ecs.instance-type =~ t2.*",
			"type": "memoryOf"
		},
		...
	]
}
```
## Strategies
They can be mixed together for a more complex strategy.
### __Binpack__
+ Optimize for cost by placing tasks on fewest possible instances to maximize resource utilization (CPU, memory).
+ Places task in the instance with least available CPU or memory (but enough to runt the task).
```json
{
// Places task in instance with least available memory
"placementStrategy": [
		{
			"field" : "memory",
			"type": "binpack"
		},
		...
	]
}
```
### __Random__
+ Place task randomly
```json
{
"placementStrategy": [
		{
			"type": "random"
		},
		...
	]
}
```
### __Spread__
+ Optimize for high availability by placing tasks as evenly as possible across your cluster
+ Tasks are spread based on specified attribute like instanceId or AZ
```java
{
//Places task evenly across different AZ's in cluster
"placementStrategy": [
		{
			"field" : "attribute:ecs.availability-zone",
			"type": "spread"
		},
		...
	]
}
```
# Amazon ECR
+ [[Docker Image#Container Registry|Container Registry]] : public and private
+ Repositories provide vulnerability scanning, versioning, encryption for images
## CLI commands
1. Login
```bash
aws ecr get-login-password --region <region> | docker login --username AWS --password <aws-account-id>.dkr.ecr.<region>.amazonaws.com
```
2. Push
```bash
docker push <aws-account-id>.dkr.ecr.<region>.amazonaws.com/<image-name:tag>
```
3. Pull
```bash
docker pull <aws-account-id>.dkr.ecr.<region>.amazonaws.com/<image-name:tag>
```

>[!note]
>Check IAM permissions if unable to execute the commands

# AWS Copilot
+ Open source CLI tool that simplifies build, deploy and operation of containerized apps on AppRunner, ECS and Fargate.
+ You declare the app architecture in a YAML manifest file, and Copilot auto-provisions all required AWS infrastructure like VPC's, load balancers, ECS clusters.
+ Can deploy to multiple environments and automate deployments using [[AWS Codepipeline]]