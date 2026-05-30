#aws #cloud 
# Prerequisite
[[Amazon SQS]]
# Overview
+ Producer wants to send one message to many subscribers (pub/sub model)
	+ Producer sends message to SNS topic.
	+ Subscribers listen to SNS topic for new messages
+ Messages not persisted. Data is lost if not delivered
+ Subscribers can be:![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_11851372.png]]
+ Producers can be:
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_11851372 (1).png]]
# Encryption
+ Encryption in transit through HTTPS/SSL.
+ Encryption at rest through KMS keys (SSE-SNS, SSE-KMS, similar to [[Amazon S3#Object Encryption|SSE-S3 and SSE-KMS]])
+ Client side encryption/decryption needs to be done by client.
# Security
+ [[IAM]] policies and SNS access policies (similar to [[Amazon S3#Bucket policy|S3 bucket policies]]).
	+ Can allow other AWS accounts and S3 services to write to a SNS topic.
# SNS FIFO
+ Similar features to SQS FIFO
	+ Order preserved through _message group id_ 
	+ Deduplication support through content based deduplication and deduplication id
+ Only SQS Standard and SQS FIFO can be subscribers
+ Limited throughput; 300 msg/s without batching, 3000 msg/s with batching
# Message Filtering
+ A subscriber can specify a message filtering policy (JSON document).
+ Only the messages which meet filter policy criteria are sent to the subscriber.![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_21437572.png]]