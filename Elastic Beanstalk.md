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
# Overview
+ [[Cloud Computing#Platform as a service(PAAS)|PAAS]] , developer centric, high level (compared to [[AWS CloudFormation]]) service.
	+ Provides a preconfigured server to run your application (Think Heroku)
	+ Automatically handles capacity provisioning, load balancing, scaling, health monitoring, instance configuration etc..
	+ User only needs to provide application code.
	+ Uses CloudFormation templates, under the hood for provisioning.
+ Full control over configuration, if you want to configure. It is not a requirement.
+ Free service, user pays for deployed instances.
+ Interact with it using AWS management console, AWS CLI, or EB CLI.
+ IAM permissions through [[AWS CloudFormation#^1b71a7|service roles]].
+ Supported Platforms
	+ Languages: Go, Java, Node,js, PHP, Python, Ruby
	+ App servers: Tomcat, Docker
# Components
+ __Application__: Collection of Elastic Beanstalk components (environments, versions, configurations,...)
+ __Application Version__: Application source code version. All versions stored in S3
+ __Environment__: 
	+ Collection of AWS resources running a application version
	+ Can create multiple environments (dev, test, prod)
	+ Can clone a environment
	+ Two types of environments:
		+ Web Server environment: The ELB load balances requests to the web servers.![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_11851294.png]]
		+ Worker environment: Messages are put into the SQS queue and the workers pull messages from the queue for processing.![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_11851294 (1).png]]
## Application Deploy workflow
+  Package code as zip and describe dependencies (requirements.txt for Python, package.json for Node.js)
+ Upload zip file and then deploy
![Elastic Beanstalk workflow.](https://docs.aws.amazon.com/images/elasticbeanstalk/latest/dg/images/clearbox-flow-00.png)
# Deployment Modes
## Environment creation
### __Single instance__
+ One EC2 instance (managed by an auto scaling group) with an Elastic IP in one AZ.
+ Optionally, deploy a RDS database.
+ Simple but not highly available, suitable for development purposes.![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_11851294 (2).png]]
### __High availability__:
+ Load balancer distributing traffic across multiple EC2 instances in multiple AZ's.
+ EC2 instances managed by an auto scaling group.
+ Optionally, Multi-AZ RDS database instance with one master and standby instances.![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_11851294 (3).png]]
## Environment updates
### __All at once__
+ Fastest deployment (Stop all instances and deploy new version on them)
+ Application has some downtime.
+ Great for quick iterations in dev environment
+ No additional cost
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_11851300.png]]
### __Rolling__
+ Updates deployed one batch / bucket at a time.
+ Can change bucket size.
+ Some downtime, but limited to a single batch
+ Application runs below capacity i.e. if ASG has 4 instances, for some period of time there will be fewer instances running.
+ No additional cost
+ Longer deployment time, since updates happen in batches.
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_11851300 (1).png]]
### __Rolling with additional batches__
+ Updates happen in batches but application runs at full capacity
+ Can change batch size
+ Small additional cost for the extra instances in additional batch. The extra instances are terminated once deployment finishes.
+ Longer deployment time than rolling
+ Good for prod
+ Some downtime, but limited to a single batch
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_11851300 (2).png]]
### __Immutable__
+ Slower deployment than rolling with additional batches
+ Application deployed to new instances in a temporary ASG, then transferred to original ASG and previous instances are terminated.
+ High cost, runs at double capacity
+ Quick rollback in case of failure (terminate new ASG)
+ Great for prod
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_11851300 (3).png]]
### __Blue Green Deployment__
+ Zero downtime, slow deployment
+ Not a selectable option, per se
+ Create a new environment (green) and deploy new version there.
	+ Use [[Amazon Route 53#Weighted Routing|weighted routing]] to split traffic between new and old environment.
	+ Observe new deployment for a specified period of time
	+ Swap environment URL's (in Elastic Beanstalk actions) when done with the test and destroy the old one.
+ Quick rollback by destroying new environment, if any issues.
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_11851300 (4).png]]
### __Traffic splitting__
+ New application deployed on temporary ASG with same capacity.
	+ Use weighted routing to split traffic between new and old ASG.
	+ Monitor health for some period of time
	+ New instances are migrated from temporary ASG to the original one and older instances destroyed.
+ Quick rollback, in case of failure.
+ Zero downtime, slow deployment
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_11851300 (5).png]]
# Lifecycle Policy
+ Elastic Beanstalk can store at most 1000 application versions.
+ To phase out old versions, use lifecycle policy
	+ Delete based on age (old versions deleted first) or space (when you have too many versions)
	+ Versions are deleted in both Elastic Beanstalk and S3 by default.
+ Currently used versions are not deleted.
+ Option to not delete source bundle in S3 to prevent data loss.
# Extensions
+ Configuration options for Elastic Beanstalk can be set within the source code instead of on UI/console.
	+ All config should be in `.ebextensions/` directory in the root of your source code.
	+ Config files should be in JSON/YAML format and have `.config` extension.
+ Can set default settings and add resources such as RDS, Elasticache, etc..
+ Resources managed by `.ebextensions` are deleted when environment is deleted