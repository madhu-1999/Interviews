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
+ __Real time__ monitoring of AWS resources, (performance, health and utilization).
+ Most AWS services provide basic metrics free of charge. Detailed monitoring can be enabled for a charge.
# Terminology
## Metric
+ It is a variable to monitor (Ex: CPU usage of a EC2 instance).
	+ The data points that represent value of variable changing over time can come from any application being monitored.
+ Exists only in the region in which they are created.
+ Cannot be deleted
+ All metric data points have a timestamp associated with it. (Recommended UTC)
	+ Timestamp can be anywhere between 2 weeks in the past to 2 weeks in the future.
+ Uniquely defined by a name, namespace and dimensions.
	+ A dimension is a key/value pair that is an attribute of a metric. (Ex: InstanceId, Environment)
	+ Can assign up to 30 dimensions to a metric
### __Custom metrics__
+ Ex: RAM usage, no of logged in users etc..
+ Use API _PutMetricData_ (CLI _put-metric-data_)
	+ _StorageResolution_ param: how often to push metric data to CloudWatch
		+ Standard: 1 minute
		+ High Resolution: 1/5/10/30 s (Higher cost)
## Namespace
+ Container for CloudWatch metrics.
+ Metrics in different namespaces are isolated from each other so that they are not aggregated into the same statistic.
+ Specified when publishing a data point to CloudWatch.
## Statistic
+ Metric data aggregation over a period of time, provided by custom metrics or AWS services.
+ Uniquely defined by a namespace, metric name, dimensions and unit of measure.
	+ Unit of measure: Bytes, Seconds, Count, Percent etc..
# EC2 Detailed Monitoring
+ Basic monitoring: data points for a metric received once every 5 minutes, and for detailed monitoring: data points for a metric received every 1 minute.
	+ Use detailed monitoring if you want ASG to scale faster
+ Free tier allows 10 detailed monitoring metrics

> [!note]
 EC2 Memory usage is not pushed as a metric by default. It must be pushed to CloudWatch as a custom metric, from inside the instance.

# CloudWatch Logs
+ Log expiration policy: never, 1-10 years
+ Logs are encrypted by default
	+ Can setup KMS encryption with own keys too.
## CloudWatch logs for EC2 or on-premises server
+ Need to install CloudWatch Agent onto EC2 instance / on-premise server to push log files
	+ CloudWatch Log Agent: old version, can only send CloudWatch logs
	+ CloudWatch Unified Agent: logs + additional system level metrics such as RAM, processes, disk metrics etc..
+ IAM permissions needed for EC2 instance / on-premise server to push logs to CloudWatch

## Sources
+ [[Interacting with AWS Services#AWS SDK|SDK]], CloudWatch Unified Agent, CloudWatch Log Agent
+ [[Elastic Beanstalk]]: Collection of logs from application
+ [[Amazon ECS]]: Logs from containers
+ [[AWS Lambda]]: Logs from functions
+ [[Amazon VPC#VPC Flow logs|VPC Flow Logs]]
+ CloudTrail based on filter
+ [[Amazon Route 53]]: Log DNS queries
## Destinations
+ [[Amazon S3]]
+ [[Other AWS Services#Amazon Kinesis Data Streams|Kinesis Data Stream]]
+ [[Other AWS Services#Amazon Data Firehose|Amazon Data Firehose]]
+ [[AWS Lambda]]
+ AWS OpenSearch
## Log Stream
Sequence of log events that share the same source.
## Log Group
Collection of log streams with the same retention, monitoring or access settings.
## Log Insights
+ Search and analyze historical log data stored in CloudWatch
	+ Ex: find a given IP in a log, count occurrences of ERROR in your logs
+ Provides a query language
	+ Filter based on fields/conditions, aggregate statistics, sort, limit
	+ Metric filters only publish data points for events that happen after filter was created. (Specify up to 3 dimensions for the metric filter)
	+ Save queries and add to CloudWatch Dashboards
+ Can query multiple log groups in different AWS accounts
## S3 Export
+ Batch export logs into S3 (_CreateExportTask_)
	+ Log data can be exported after up to 12 hours since arrival
## Log Subscriptions
+ Real time data streaming of logs
+ Send data stream to Kinesis Data Streams, Data Firehose or Lambda
+ Filter which log events to send using subscription filter![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_11851352.png]]
### __Cross Account / Cross Region Log Subscription__
+ Send log events to resources in different AWS accounts
	+ The access policy of destination resource must allow log transfer from source account.
+ Create an IAM role in destination account that allows writing into destination resource.
	+ Ensure this role can be assumed by source account.
	![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_11851352 (1).png]]
# Alarms
+ Trigger notification/changes to resources for any metric (Ex: CPU usage > 70%)
	+ Can also watch metrics in other AWS accounts
+ Alarm states:
	+ OK: Metric within threshold
	+ INSUFFICIENT_DATA: Not enough data available to calculate metric
	+ ALARM: Metric out of threshold
+ Alarm targets:
	+ Stop, Terminate, Reboot or recover an EC2 instance
	+ Trigger Auto-Scaling action
	+ Send notification to SNS
## Alarm types
1. Metric alarm: Based on some metric
2. Composite alarm: Alarm that monitors other alarms (metric or composite) through a rule expression. All expressions in rule must be true for alarm to go off.
## Evaluating an Alarm
+ __Period__: Amount of time it takes to evaluate a metric or expression.
+ __Evaluation period__: No of most recent periods to consider when evaluating an alarm.
+ __Datapoints to alarm__: No of data points within a evaluation period that must be breaching the metric to result in an evaluation of ALARM.
# CloudWatch Synthetics Canary
+ Configurable script to monitor API's, URL's, Websites etc..
	+ Can check latency and availability of endpoints
	+ Can store screenshots of UI and latency data
	+ Can Reproduce actual user actions programmatically (Kinda like LogRocket)
+ Integrates with CloudWatch alarms
+ Scripts in Node.js or Python
+ Can run once or on a fixed schedule
## Use case
+ CloudWatch Synthetics Canary is monitoring application in an EC2 instance in _us-east-1_.
+ If application fails, it triggers a CloudWatch Alarm which in turn invokes a lambda function that changes Amazon Route 53 DNS record to point to application deployed in _us-west-2_.![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_36527940.png]]
# Amazon EventBridge 
+ __Serverless event bus__ that facilitates event-driven architecture, by allowing different applications to communicate using events.
+ Autoscales to process large number of events.
+ Supports __real-time__ processing of events, so that applications can instantly react to changes.
+ __Pay as you go__ service. Only pay for the events that you publish and process.
## Key Components
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_19730070 1.png]]
### __Event__
+ Any change in a system, data or environment (ex: upload in a s3 bucket, new user signup in an application)
### __Event Sources__
+ Publisher of events. Can be a AWS service, your custom application or partnered third-party apps.
### __Targets__
+ The resource or endpoint that processes events delivered by EventBridge.
+ Can be AWS service, your custom application or partnered third-party apps.
### __Event Bus__
+ Primary router for events. Receives events from all sources sending events and delivers them to targets.
	+ Default bus: Receives events from all AWS services in your account.
	+ Partner event bus: Receives event from integrated third party apps like ZenDesk, DataDog,
+ Which event goes where is determined by __rules__, which filter events based on defined criteria or schedule.
+ Event buses can be accessed by other AWS accounts by using a [[IAM#Policy|resource-based policy]].
+ Can archive and replay events
### __Pipe__
+ Router for events between a __single source and single target__.
+ Can optionally filter, transform or enrich events before delivering to target.
### __Schema Registry__
+ Schema is JSON object defining event fields and structure.
+ Schema Registry automatically discovers and stores all event schemas.
	+ Generate code bindings in different programming languages to ensure consistency.
	+  Validation for schemas.
### __Scheduler__
+ Automate triggering of events at scheduled times or intervals using cron expressions. (Ex: backup, cleanups).