#system-design #hld 
# Overview
+ Communication mechanism to enable different parts of a system to send and receive messages asynchronously.
+ It acts as an intermediary that holds messages sent from producers (or publishers) and delivers them to consumers (or subscribers).
# Components 
![](https://substackcdn.com/image/fetch/$s_!Kg04!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fb6eab95c-f56f-45c7-9248-7e6070c1979b_1614x702.png)
## Producer / Publisher
The entity that sends messages.
## Consumer / Subscriber
The entity that consumes or processes the messages.
## Queue
The data structure used to store the messages.
## Broker / Queue Manager
Service that:
	manages the message queue
	handles delivery of messages
	ensures that messages are routed correctly between consumer and producer
## Message
Unit of data stored in the queue. Contains:
	payload (actual data)
	metadata (headers, timestamps, priority)
# Workflow
1. Publisher generates and sends message to the queue.
2. Queue stores message in a persistent or transient manner depending on config and sends an ACK to the publisher.
3. Consumer retrieves message from queue for processing. Messages can be consumed in order, by priority or in parallel.
4. Consumer sends an ACK to queue after processing message(s), and those entries are removed from the queue.
# Types of Queue
## Point-to-point (P2P) Queue
+ Messages sent by one producer to one consumer.
+ Ex: task processing system
## Pub / Sub Queue
+ Messages are published to a **topic**, and multiple consumers can subscribe to that topic to receive messages.
+ Ex: Notification system
## Priority Queue
+ Messages in the queue are assigned **priorities**, and higher-priority messages are processed before lower-priority ones.
## Dead Letter Queue
+ A special type of queue where messages that cannot be processed (due to errors or retries) are sent. Ex: Message retried say, 3 times but failed, so it was sent to DLQ.
+ Useful for troubleshooting and handling failed messages.![](https://substackcdn.com/image/fetch/$s_!obv9!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F2ec7dc65-448d-4880-947e-0f8a15bdce14_1592x712.png)
## Push vs Pull
+ In pull queues, consumers __pull__ messages from the queue for processing.
+ In push queues, the queue __pushes__ messages to the consumers for processing.
![](https://substackcdn.com/image/fetch/$s_!JZom!,w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F6e8ea699-e887-4676-b3ca-c0f1a814a3ba_800x471.png)

# Duplicate Messages
## Causes of duplicate messages
+ Network issues like packet loss or timeout, can cause messages to be retransmitted to the queue.
+ Suppose queue waits for ACK from consume, but the wait period times out. Then, the queue may send the same message to the consumer for processing, even though the message was delivered, but ACK was lost.
+ If consumer crashes before sending ACK, it may reprocess the same message after coming back up.
+ Message broker may fail, causing retransmission of message by publisher to queue or from queue to consumer.
## Challenges in handling duplicate messages
+ Consumer must ensure that processing a duplicate message results in the same output. i.e. the operation must be __idempotent__.
+ Consumer should track which messages were already processed, in case of failure or retransmissions. This requires a separate storage mechanism.
+ Preventing duplication of messages adds a significant performance overhead.
+ Messages can arrive out of order or delayed, posing a significant challenge to maintaining consistency while preventing processing of duplicate messages.
+ As systems scale, tracking and preventing processing of duplicates becomes harder to do without creating a bottleneck, due to the significant amount of data.
## Handling duplicate messages
### __At least once delivery__
+ Guarantees delivery of a message to consumer at least once.
	+ After sending message to consumer, retries if ACK is not received within a specified timeout period. Can lead to duplicate messages.
	+ Application layer must check if a message is a duplicate.
+ Prioritizes reliability over preventing duplicate messages.
+ Use when it is unacceptable to lose messages. (ex: logging)
### __At most once delivery__
+ Ensures a message is delivered at most once or not at all.
	+ Message sent to consumer once. No retries if ACK is not received within specified timeout period.
+ Prevents duplicates but compromises durability (loss of message).
+ Use when it is acceptable to lose messages (ex: non-critical notifications or updates)
### __Exactly once delivery__
+ Ensures a message is processed exactly once, with no duplicates or losses.
	+ Consumer operations are designed to be idempotent.
	+ Messages are assigned a unique id or hash and processed messages are stored durably in a separate storage mechanism.
+ Use when data integrity is paramount (ex: financial transactions, order processing)
+ Most complex approach and significant performance overhead.

# Advantages
+ Allows components to communicate without being aware of the other's existence. It __decouples__ the architecture.
+ Asynchronous communication between components, improves system __throughput__.
+ Multiple consumers can pull messages from the queue, allowing work to be __load balanced__.
+ Messages are not lost even if a consumer or producer fails. Allows for __retries__ and __error handling__.
+ Can handle a huge amount of messages and __scale horizontally__ by adding more consumers.
+ Acts as a __buffer__ (rate limiting) and prevents consumers from being overwhelmed.
+ Messages are not lost, even if queue crashes, since __messages are stored on disk__, and ___NOT___ on RAM.
# Further Reading:
https://systemdesignschool.io/blog/message-queue