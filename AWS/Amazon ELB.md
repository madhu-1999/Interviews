#aws #cloud 
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
[[Scaling#Load Balancer|Load Balancer]]
# Overview
+ Elastic Load Balancer
+ [[Storage in AWS#Managed Services|Managed Service]]. 
	+ AWS responsible for upgrade, maintenance of underlying infrastructure and high availability
	+ Customer responsible for setting security rules, configuration of load balancers & auth.
+ As the number of [[Amazon Elastic Compute Cloud (AWS EC2)|EC2]] instances fluctuates in response to traffic demands, incoming requests are first directed to the load balancer. From there, the traffic is distributed evenly across the available instances.
+ Serves as the single point of contact for all incoming web traffic to an [[Amazon Elastic Compute Cloud (AWS EC2)#Auto Scaling Group (ASG)|Auto Scaling group]].
+ **Decouples** front-end and backend tiers and reduces manual synchronization.
+ Provides SSL termination (HTTPS) for websites.
+ Can be setup as internal (private) or external (public) ELB's. 
+ Each ELB has a **static DNS name**, which clients can use to send traffic to.
+ Operates within a [[AWS Global Infrastructure|region]] but provisions nodes in each AZ that it is enabled in.
	+ Load balancing done only for instances in target group that are in AZ's where ELB is enabled.
+ Monitors health of targets. If target becomes unhealthy, traffic is not sent its way. If an AZ fails or has no healthy targets, traffic is routed to healthy targets in other enabled AZ's.
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_11851240.png]]
# Listener
+ A listener is configured with 
	+ a frontend protocol and port (client-to-load-balancer) 
	+ a backend protocol and port (load-balancer-to-target)
+ Ex:  HTTPS listener on port 443 that forwards traffic to target group using HTTP on port 80.
+ Listener rules determine which traffic is routed to which [[#Target Groups|target group]].
	+ Each rule has conditions, priority and actions.
+ Every listener has a default action that is performed if no condition is met. Ex: forward traffic to a specific target group.
+ Can specify one of 3 actions for a rule:
	+ Forward to a target group
	+ Redirect to URL
	+ Return fixed HTTP response.
+ If the listener protocol is HTTPS or TLS, you must deploy at least one SSL/TLS server certificate on the listener. This is used to terminate and decrypt requests from clients before they are sent to the targets.
# Target Groups
+ Used to define a set of instances (targets) to which traffic should be routed by load balancer based on listener rules.
	+ Registered targets can be in different AZ's.
	+ Target type is specified when creating a target group (cannot be changed later).
		+ `instance` : specified by instance id
		+ `ip` : target is IP address (cannot use public ip). 
		+ `lambda` : target is a lambda function.
+ A [[#Listener|listener rule]] is used to evaluate incoming traffic and forward it to a designated target group if its conditions are met.
	+ When you create a listener rule, you specify a target group and conditions.
+ Can register a target with multiple target groups.
+ Can configure health checks on a target group basis. 
	+ Health checks are performed on all targets registered to a target group.
	+ A target group is only actively monitored for health if it is referenced by a listener rule.
# Routing algorithms
+ Slow start duration is used to specify a period of time where a newly registered target receives a lower number of requests, giving them time to ramp up. After it expires, it receives request at full capacity. 
	+ Not compatible with **Least outstanding requests** and **Weighted random**
1. **Round Robin**
	+ Routes requests in a set rotation.
	+ Used when requests being received are similar in complexity or targets have similar processing ability.
	+ Default routing algorithm.
2. **Least outstanding requests**
	+  Routes requests to the targets with the lowest number of in progress requests. 
	+ Used when the requests being received vary in complexity, the registered targets vary in processing capability.
3. **Weighted random**
	+ Routes requests across targets in random order.
	+ Cannot be used with sticky sessions.
# Load Balancer Types
AWS provides 4 types:
## Classic Load Balancer (Deprecated)
+ Old generation
+ Routes traffic at transport layer (TCP/SSL), or at the application layer (HTTP/HTTPS).
+ For TCP listeners, it uses a round-robin algorithm. For HTTP/HTTPS listeners, it uses the least outstanding requests algorithm.
+ Can distribute traffic evenly across all enabled AZ's.
+ Can route requests to ports.
	+ Configure a listener that defines a **front-end** port and **back-end** port. 
	+  If you create a CLB listener that routes traffic from the front-end port `80` to the back-end port `8080`, every EC2 instance registered with that CLB must be running an application on port `8080`.
	+ This means you **cannot run multiple applications** on different ports on a single instance, since a CLB can only route to one back-end port.
## Application Load Balancer
+ New generation
+ Routes traffic at application layer (HTTP/HTTPS).
+ Need to enable in atleast 2 AZ's.
+ Cross-zone load balancing by default i.e. load balanced across instances in multiple AZ's.
+ Uses **listeners** and **rules** to route requests to **target groups**.
	+ can define rules based on the request's path (e.g., `/app1` or `/app2`), host header, or other information.
	+ The path pattern rules apply only to the URL path and not to the query parameters of the URL.
+ Can register EC2 instances, ECS tasks (`ip`) and lambda functions as targets.
+ Targets don't see IP of client directly, it is in `X-Forwarded-For` header.
+ Can route requests to ports on one or more requested targets in [[Amazon VPC|VPC]].
	+ Can register the same target (such as an EC2 instance) with a target group multiple times, using a different port each time.
	+ This means we can run multiple applications or microservices on different ports on a single EC2 instance.
	+ Ex: configure a single listener on port `80` with two rules:
		> **Rule 1**: IF path is /app1* THEN forward to target group for port 8080
		   **Rule 2**: IF path is /app2* THEN forward to target group for port 9090

![The components of a basic Application Load Balancer](https://docs.aws.amazon.com/images/elasticloadbalancing/latest/application/images/component_architecture.png)
## Network Load Balancer
+ New generation
+ Routes traffic at transport layer(TCP/UDP/TLS) based on IP addresses and port numbers.
+ **Highest performance and lowest latency**.
+ Provides static IP for each AZ.
+ Ideal for real-time applications and high volume traffic workloads.
+ Can register EC2 instances, private IP addresses, and even other ALBs as targets.
+ Designed for single-zone application architectures but can fail over to other healthy AZs if one becomes unavailable.
## Gateway Load Balancer
+ New generation
+ Operates in Layer 3 (IP).
+ Facilitate the deployment, scaling, and management of a fleet of virtual network appliances (such as firewalls, intrusion detection systems, and deep packet inspection systems).
+ Traffic is routed as is by the GWLB to the target group containing security appliances. The security appliances then analyze the request to determine if it is compliant/safe, and if safe, returns it to the GWLB to route it to its destination.![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_28875086.png]]
# Cross-Zone Load Balancing
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_19733688.png]]
+ [[#Application Load Balancer|ALB]]
	+ Enabled by default
	+ No charges for inter-AZ data
+ [[#Network Load Balancer|NLB]] and [[#Gateway Load Balancer|GWLB]]
	+ Disabled by default
	+ Charges for inter-AZ data
+ [[#Classic Load Balancer (Deprecated)|CLB]]
	+ Disabled by default
	+ No charges for inter AZ data
# Server Name Indication (SNI)
Read [[SSL & TLS]] before continuing.
+ Allows ELB to securely host multiple websites with different TLS/SSL certificates on same address and port.
+ Works with [[#Application Load Balancer|ALB]] and [[#Network Load Balancer|NLB]].
## How they work together
+ An ELB acts as a single point of contact for multiple web servers, distributing incoming traffic across them. With SNI, it also handles certificate management.
+ All certificates are stored in Amazon Certificate Manager (ACM).
Here is the process:
1. **Client request**: A user's browser sends a secure request to the ELB's IP address. The request includes an SNI extension that specifies the hostname of the target website (e.g., `www.site1.com`).
2. **ELB presents the correct certificate**: The ELB receives the request on its HTTPS listener, which has multiple certificates associated with it. The ELB looks at the hostname in the SNI field and selects the correct TLS/SSL certificate for that domain to send back to the client.
3. **Encrypted traffic**: The client and ELB complete the TLS handshake and establish a secure, encrypted connection.
# Sticky sessions
+ Allows load balancer to bind a user session to a specific target in a target group.
+ On enabling, routing algorithm is used to select initial target, then future requests are automatically forwarded to same target.
+ Works for [[#Classic Load Balancer (Deprecated)|CLB]], [[#Application Load Balancer|ALB]] and [[#Network Load Balancer|NLB]].
+ Cookie is used to enforce stickiness and has an expiration date you control
+ Used when user should not lose session data.
+ May bring imbalance to load distribution.
+  Cannot be turned on if cross-zone load balancing is off.
## Cookie Types
### **Application-based cookies**
1. Custom cookie
	1. Generated by target
	2. Custom attributes for application
	3. Name must be specified for each target group.
	4. Cannot use **AWSALB**, **AWSALBAPP** and **AWSALBTG** as names.
2. Application cookie
	1. Generated by load balancer
	2. Cookie name is **AWSALB** for ALB and **AWSELB** for CLB.
### **Duration-based cookies**
+ Generated by load balancer
+ Cookie name is **AWSALB
# ELB + ASG
+ ASG is attached to target group and target group is attached to load balancer.
+ ASG automatically registers new instances (creating during scale out) with the load balancer and also deregisters instances (destroyed during scale in).
+ ELB health checks can be passed to ASG, which can terminate instances deemed unhealthy by the load balancer.
## How they work together
**Low-demand period**: 
![[ELB_LowDemand.png]]
**High-demand period**: ASG scales up to meet demand.![[ELB_HighDemand.png]]
**Load Balancing**: directs the incoming traffic evenly to different web servers based on their current load.![[ELB_Traffic.png]]
# Connection draining
+ Also called deregistration delay.
+ Mechanism to allow a instance to shut down gracefully without abruptly terminating active user connections.
## How it works
1. **Initiate draining**: An instance is marked for removal from a load balancer's backend pool. This can happen due to various reasons, such as a manual removal, an autoscaling event, or a failed health check.
2. **Stop new connections**: Once marked, the load balancer stops routing any new requests to the instance.
3. **Allow existing connections to finish**: For a specified period, known as the "draining timeout," the load balancer keeps existing connections to the instance open. This allows active, in-flight requests to complete their processing.
	+ Draining timeout is configurable between 1 and 3600 seconds. Default 300. 
4. **Forcibly close remaining connections**: If the draining timeout expires and any requests are still in progress, the load balancer will forcibly terminate those connections.
5. **Remove instance**: After all active requests are completed or the timeout is reached, the instance is completely removed from the backend pool and can be safely terminated.
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_19733714.png]]
# Steps to create ELB
1. Choose [[#Load Balancer Types|load balancer type]]
2. Choose scheme
	1. Internet-facing/public: Route traffic from clients over the internet to targets. Requires a public subnet.
	2. Internal/private: Route traffic from client to target using private IP addresses.
3. Choose IP address type: IPv4 or Dualstack (IPv4 and IPv6 addresses)
4. Select [[Amazon VPC|VPC]] and AZ's
5. Assign security group
6. Create [[#Listener|listeners]] : specify protocol, port and default action.
	1. After creation, can specify rules in created ELB's listener tab.
# Workflow recap
1. A user's browser sends a secure request to the static DNS of the **ELB**. It includes an SNI extension specifying hostname of target website.
2. The **ELB** receives the request on its HTTPS listener and presents the correct certificate to the client based on hostname provided.
3. The client and **ELB** complete the TLS handshake and establish a secure, encrypted connection.
4. The **ELB** receives the request, decrypts it, and, based on its listener rules, forwards the traffic to a specific **target group**.
5. The **target group** contains the list of all currently registered, healthy EC2 instances maintained by the **Auto Scaling Group**.
6. The **ELB** selects a healthy instance from that list and forwards the request to it.
7. If a new instance is needed, the **ASG** launches one based on its scaling policy and configuration.
8. The **ASG** registers this new instance with the **target group**.
9. The **ELB** immediately sees the new, healthy instance in the target group and begins routing traffic to it.