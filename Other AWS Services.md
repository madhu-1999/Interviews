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
# Amazon Kinesis Data Streams
+ Collect and store __real-time__ streaming of data![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_26101780.png]]
+ Data Retention upto 365 days. (default: 24 hrs)
	+ Data cannot be deleted. Need to wait for it to expire.
+ Consumers can replay or reprocess the data stored in the stream
+ Data up to 1 MB
+ Data ordering guaranteed using Partition id.
	+ Data with same partition id will be in order
+ At rest KMS encryption, HTTPS/SSL encryption in transit
+ Use Kinesis Producer Library (KPL) for optimized producer application.
+ Use Kinesis Consumer Library (KCL) for optimized consumer application
+ Use cases: Real time big data analytics, ETL
## Capacity modes
### __Provisioned mode__
+ Choose number of shards.
	+ Each shard gets 1MBps in (1000 records/s)
	+ Each shard gets 2MBps out
+ Scale manually to increase or decrease number of shards
+ You pay per shard provisoned per hour
### __On-demand__
+ No need to provision anything
+ Default capacity provisoned: 4MBps in
+ Scales automatically based on past 30 days observed throughput peak
+ Pay per stream per hour and for data in/out per GB
# Amazon Data Firehose
+ __Near-real time__ streaming of data
	+ Data is stored in a buffer and flushed periodically to consumer (based on size of buffer or time).
	+ ___DOES NOT___ store data
+ Fully managed service
	+ Auto scaling, serverless, pay as you go
+ No replay capability
+ Supports JSON, CSV, Parquet, Avro, Raw Text, Binary Data
	+ Optional data transformation through AWS Lambda
+ Any failed data can be sent to a S3 bucket![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_26101802.png]]
# Managed Service for Apache Flink
+ Framework for processing data streams (Java, SQL, Scala)
+ Producers: Amazon MSK (Kafka) and Amazon Kinesis Data Streams
+ Managed service
	+ Provisioned cluster capacity and compute + auto scaling
	+ Application backups (checkpoint + snapshot)
	+ Use any Apache Flink features to transform data
# AWS AppSync
+ Serverless service to build scalable GraphQL APIs.
+ It allows applications to securely access, combine, and manipulate data from multiple sources, such as databases, microservices, and AI models through a single network request.
+ Retrieve data in __Real time__ with Websocket or MQTT on Websocket.
+ Offline data synchronization for mobile and web apps
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_19732092.png]]
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_19732092 (1).png]]
+ Four ways to authorize client to interact with AppSync:
	+ _API_KEY_ 
	+ [[IAM|_AWS_IAM_]]
	+ _OPENID_CONNECT_
	+ [[Amazon Cognito|_AMAZON_COGNITO_USER_POOLS_]]
# AWS Amplify
+ Set of tools to create full stack web and mobile apps.
	+ [[Elastic Beanstalk]] used to deploy apps.
	+ Must have features like data storage, auth, ML are powered by AWS services.
	+ Frontend libs with ready to use components for React.js, Vue.js, Android, Flutter, iOS.
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_30055484.png]]
+ Authentication
	+ Uses [[Amazon Cognito]]
	+ `amplify add auth`
+ Datastore
	+ [[#AWS AppSync]] and [[Amazon DynamoDB]].
	+ Work with local data and auto-syncs to cloud.
	+ `amplify add api`
+ Hosting
	+ Build and host web apps
	+ CI/CD
	+ PR reviews
	+ Custom domains
	+ Monitoring
	+ `amplify add hosting`
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_30055484 (1).png]]
+ End-to-end testing
	+ Integrated with Cypress framework
	+ Run test commands at build time (amplify.yml).
	+ Run tests while app is being deployed (e2e tests)