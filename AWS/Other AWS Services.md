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
+ Data Retention upto 365 days.
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