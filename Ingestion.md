	#de 
# Definition
**Data ingestion** is the process of collecting, moving or onboarding data from one or more sources into a target system, transforming it as needed for downstream use.
# Ingestion Patterns
## Push Based Ingestion
 Source systems actively send data to the target as it becomes available. 
 The source controls when and how data is transferred, often using webhooks, APIs, or message queues.
 ### __Scalability Concerns__
 ### 1. Vertical vs. Horizontal Scaling Limits

When a producer pushes more data than a single consumer instance can handle, you have two choices:

- **Vertical Scaling:** Increasing the CPU or RAM of the consumer. This is a temporary fix and has a hard ceiling.
    
- **Horizontal Scaling:** Spinning up more consumer instances. However, in a push model, this is harder because the **broker** must be aware of the new instances and manage the distribution logic (e.g., Round Robin or Load Balancing) to ensure they aren't all sent the same message.
	- Altenatively, we can add more workers to scale processing of queue messages. Broker need not be aware.
### 2. The "Resource Exhaustion" Trap

In a push system, the consumer's resources (thread pool, memory, and open connections) are tied directly to the incoming request volume.

- **Memory Pressure:** If the consumer starts buffering incoming pushed messages because it can't process them fast enough, it risks an **Out of Memory (OOM)** error.
    
- **Thread Starvation:** Each push request usually occupies a worker thread. If the processing logic is slow (e.g., writing to a slow database), all available threads quickly become blocked, causing the consumer to reject new messages.
### 3. Implementing Backpressure Manually

Since push systems don't have native backpressure (the producer doesn't know you're struggling), the consumer must implement its own defense mechanisms to scale safely:

- **Rate Limiting:** The consumer must explicitly tell the producer to "slow down" or return a `429 Too Many Requests` status code.
    
- **Circuit Breakers:** If the consumer’s downstream dependencies (like a database) fail, the consumer should "trip" the circuit to stop accepting pushes until the system stabilizes.
4. Throughput Bottlenecks
Push models generally favor **low latency** over **high throughput**.

- **Individual Delivery:** Messages are typically pushed one-by-one. This prevents the consumer from using "batch processing" techniques (common in [pull-based ingestion](https://blog.stackademic.com/push-vs-pull-based-consumers-what-they-are-and-when-to-use-them-0ba209d82840)), where you might process 1,000 records in a single database transaction.
    
- **Network Overhead:** Thousands of individual HTTP/TCP handshakes consume more network resources on the consumer side than a few large batch pulls.
## Pull Based Ingestion
The target system periodically queries or polls source systems to retrieve new data. The target controls the ingestion schedule and determines when to fetch updates.
## Push vs Pull

| __Feature__              | __Push__                                                                    | __Pull__                                                                    |
| ------------------------ | --------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| __Latency__              | __Low__ as messages are delivered in real-time                              | __Higher__ as latency = polling interval + processing time                  |
| __Backpressure oontrol__ | __Limited__ as system can be flooded with messages if it doesn't scale well | __Strong__, as system decides when and how much to pull                     |
| __Simplicity__           | Simple                                                                      | More complex (batch how mcuh, offset etc..)                                 |
| __Retry control__        | __Harder__, since broker controls retries                                   | __Easier__ since system controls when to acknowledge.                       |
| __Scalability__          | Harder, must configure push rate                                            | __Easier__, add more workers to process since rate is controlled by system. |
# Extraction Types
## Full Extraction
The process simply connects to the source and pulls __all available records__ for the tables or files you're interested in.
If the target system already contains data from a previous run, a full extraction typically involves __deleting (truncating) the old data__ before loading the newly extracted complete dataset.
### **When to Use Full Extraction:**
- **Initial Data Load:** When you first set up your ETL pipeline and need to populate the target system (like a data warehouse) for the very first time, a full extraction is usually necessary.
- **Small Datasets:** If the source dataset is relatively small (think thousands or maybe tens of thousands of records, depending on your systems), the overhead of a full extraction might be negligible. It's often simpler to implement than incremental logic.
- **Source System Limitations:** If the source system doesn't provide a reliable way to track changes (like timestamps or version numbers), full extraction might be your only option.
### **Drawbacks of Full Extraction:**
- **Performance:** Extracting large volumes of data can be slow and consume significant computing resources on both the source and ETL systems.
- **Network Load:** Transferring large datasets across a network can cause congestion and take a long time.
- **Source System Impact:** Running large queries frequently can put a heavy load on the source database or application, potentially impacting its performance for operational users.
- **Time Consuming:** The entire process takes longer, which might not be suitable if you need data updated frequently in your target system.
## Incremental Extraction
Instead of grabbing everything, it identifies and extracts only the data that has been added or modified since the last extraction run. Common techniques to track changes are:
- **Timestamps:** Many database tables have columns that record when a row was last modified (e.g., `last_updated_at`, `modification_date`). The ETL process records the timestamp of the last successful extraction and, on thue next run, only extracts rows with a timestamp later than the recorded one.
- **Version Numbers or Sequence IDs:** Some systems assign incrementing numbers or unique IDs to transactions or records. You can track the last processed ID and fetch only records with higher IDs.
- **Status Flags:** Sometimes, records have a status flag (e.g., `is_processed`, `needs_sync`) that indicates whether they need to be extracted. The ETL process extracts records with a specific flag and potentially updates the flag after successful extraction.
- **Change Data Capture (CDC):** This is a more advanced set of techniques where the source database itself logs changes (inserts, updates, deletes) as they happen. Specialized CDC tools or database features can then read these logs to capture changes efficiently, often with very low latency.
### **When to Use Incremental Extraction:**
- **Large Datasets:** When dealing with tables containing millions or billions of records, extracting only the changes is much more efficient than a full extraction.
- **Frequent Updates:** If data needs to be refreshed in the target system frequently (e.g., hourly or even more often), incremental extraction is usually the only feasible approach.
- **Resource Constraints:** It reduces the load on the source system, the network, and the ETL processing engine.
- **Near Real-Time Requirements:** CDC-based incremental extraction can enable data pipelines that operate with very low delays between a change occurring in the source and it reflecting in the target.
###  **Drawbacks of Incremental Extraction:**
- **Complexity:** Implementing the logic to reliably detect changes can be more complex than simply selecting all data. You need a dependable mechanism (timestamps, IDs, CDC) in the source system.
- **Handling Deletes:** Detecting deleted records can be tricky. Timestamps often only track inserts and updates. CDC systems or specific application logic (like "soft deletes" using a flag) are typically needed to capture deletions.
- **Potential for Missed Data:** If the change detection mechanism isn't perfectly reliable or if an ETL run fails midway, there's a risk of missing some changes. Careful error handling and state management are required.
- **Dependency on Source:** The effectiveness heavily relies on the features and reliability of the source system's change tracking mechanism.

>[!info] __Delta extraction__ is  a subset of incremental extraction concerned purely with differences between two snapshots (insert/delete/update), while incremental extraction is a broader term for any mechanism that captures changes (timestamps, logs).

# CDC (Real-time incremental loading)
>[!important] CDC is real-time incremental loading for inserts/updates/deletes while Delta Extraction is batched incremental loading for inserts/updates/deletes.

Change Data Capture (CDC) is a data integration technique that ==identifies and records== row-level changes made to a dataset, such as ==inserts, updates, and deletes==. By changing only those events collected from logs, triggers, or snapshot deltas, it can form an incremental stream.
	ETL typically moves data in scheduled batches, such as hourly, nightly, or at another fixed interval. This can introduce latency and requires the system to scan entire tables or ==large partitions==, even when ==only a small portion of records have changed==.

Real-time CDC delivers the fastest data, but it’s also the most __expensive and operationally complex__. Unless you truly need sub-second latency, micro-batch is usually the more practical long-term solution.
## Use cases
In environments such as e-commerce, banking or logistics, data changes constantly as new data is created through actions like purchases, profile updates or adjusted inventory. Without CDC, those updates remain isolated in source systems until the next batch ETL job, and this can leave dashboards, reports and models to rely on outdated datasets. (__real time dashboards__)
This process also supports __zero-downtime migrations__.Instead of freezing writes or performing risky cutovers, CDC continuously replicates changes between old and new systems in order to allow seamless migrations.
CDC also __supports auditability__ and governance by preserving a clear record of how data evolved.
	If a record changes three times between your hourly batch runs, you only see the **final** state of that record. In CDC you see all 3 changes.
CDC also __supports machine learning feature pipelines__ by delivering continuous updates that keep training and inference aligned, reducing online/offline skew
## Mechanisms for CDC
### **Log-based CDC**
This process reads directly from database transaction logs such as MySQL binlog, PostgreSQL WAL or Oracle redo logs. These logs record every single transaction committed to the database.
	_Limitation_: Requires specialized software or database features to capture changes.
	_Advantage_: Because it works at the database level instead of querying live tables, it minimizes impact on production systems while still capturing all inserts, updates and deletes in real time.
### __Trigger based CDC__
[[Triggers|Database triggers]] can be created on source tables. These triggers write details about the changes (what changed, what the new/old values are) into separate _change tables_. The ETL process then extracts data from these change tables.
	_Limitation_: Triggers add overhead to the source database transactions, potentially impacting application performance. They also require careful management.
	_Advantage_: It offers precise control and can include custom logic or transformations, which can be useful for regulated workloads
### **Query-based CDC**
This method identifies modified records using timestamps or version numbers.
	_Limitation:_ Requires these columns to exist and be reliably updated by the source application. ==Doesn't inherently capture deletes== unless a 'deleted' flag or status column is also used and maintained.
## DB Specific CDC features
__SQL Server__'s native CDC features automatically capture inserts, updates, and deletes from a source table into dedicated change tables within the database.
__Oracle__ enables CDC through technologies such as LogMiner and GoldenGate, which read redo logs to detect committed changes without impacting the source workload. These tools allow high-volume, low-latency replication.
**MySQL** exposes change events through its binary log, allowing CDC tools to consume row-level updates efficiently.
**PostgreSQL** uses its Write-Ahead Log to enable logical decoding, which surfaces change events that downstream consumers can process.
## Example
In a typical architecture, a log-based CDC tool (Debezium/Attunity/Fivetran) reads change events directly from database transaction logs. 
Instead of writing these events to a target table immediately, Debezium publishes them into Kafka topics, where they become part of a durable event stream.
 Once the CDC events are in Kafka, other systems, such as data warehouses or lakehouses, store the latest data as it arrives.
	 Every `INSERT`, `UPDATE`, and `DELETE` is captured as a new row in a stream.
	 An `UPDATE` to a user's address appears as a new record with a `version_2` or a `timestamp` higher than the previous one.
	 Such an append-only pattern is preferred since updates are frequent. (Most columnar formats rely on __immutability__ => updates are costly).
 ![[Pasted image 20260505154927.png]]
## Further Reading
 https://medium.com/@richardzgyao/real-world-tips-for-building-custom-cdc-data-pipelines-7c50e42142b2
# Handling Extraction Errors
## Common Errors
- **The Source is Unavailable:** The database server might be down for maintenance, the network connection could be temporarily lost, or the file server might be offline.
- **Incorrect Credentials or Permissions:** You might be using the wrong username/password, or the account your ETL process uses might not have the necessary permissions to read the specific data you need.
- **Data Format Unexpectedly Changes:** Imagine you expect a CSV file with five columns, but today it arrives with six, or a column name changes. Or perhaps a field in a JSON response is missing or has a different data type than anticipated. This is sometimes called "schema drift."
- **Data is Missing or Corrupt:** The source file might be empty, incomplete, or contain corrupted data that cannot be read correctly.
- **Timeouts:** The source system might be slow to respond, causing your extraction process to give up after waiting too long.
- **Resource Limits:** Some sources, especially APIs, might limit how much data you can request or how frequently you can ask for it (rate limiting). Exceeding these limits can cause errors.
## How to handle
The absolute minimum you should do is **log** what happens during extraction. Logging means recording information about the process execution into a file or a dedicated logging system.
- **What to Log:** At a minimum, log when an extraction starts, when it finishes successfully, and _especially_ when it fails. If an error occurs, log the details: what went wrong, which source was involved, and any error messages received.
- **Why Log:** Logs provide a history of what happened. When an extraction fails overnight, logs are often the only way to diagnose the problem. They help you understand if errors are temporary glitches or persistent problems needing investigation.

__Retry with exponential backoff__ (timeouts, source unavailable).
If an extraction fails repeatedly, even after retries, someone needs to know about it so they can investigate. This is where **alerting** or **notifications** come in.
- **How:** This could be as simple as sending an email or a message to a chat system (like Slack) when a critical extraction fails and cannot recover automatically.
- **When:** You typically alert after exhausting retries or for errors that retries can't fix (like authentication failures).

For corrupted or missing data, we can:
- **Fail the Entire Extraction:** If data quality is critical and any error is unacceptable, you might choose to stop the entire process if even one record is bad. This prevents potentially corrupt data from moving downstream.
- **Skip the Bad Record/File:** If occasional bad records are acceptable or expected, you might log the error, skip the problematic record or file, and continue extracting the rest of the data. This keeps the pipeline flowing but requires careful examination of how missing data will impact your results.
- **Quarantine Bad Data:** A more advanced approach is to move the problematic data to a separate "quarantine" location for later inspection and potential manual correction.

To handle schema drift, there are a few strategies:
+ __Schema Evolution__: Databases like **BigQuery** and **Delta Lake** allow for schema evolution, where new columns are automatically added to the table rather than failing the job.
+ **JSON/Variant Columns:** If the source is highly unstable, load the entire record into a single `JSON` or `STRING` column. You can then use SQL in **dbt** to extract the fields you need, which prevents the pipeline from breaking when ==a field you _don't_ use changes.==
+ **Detection:** Use tools like **Great Expectations** or **dbt tests** to validate the schema before the transformation runs. If a mandatory column is missing, the pipeline should fail and send an alert.
+ __Versioning the API/Source__: If you are scraping or using an API, always lock into a specific version (e.g., `v2`). This ==doesn't stop schema drift entirely==, but it ensures that the source team doesn't push breaking changes to your endpoint without a version bump