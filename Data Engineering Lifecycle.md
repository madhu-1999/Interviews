---
tags:
  - "#de"
---
# Stages
![Data Engineering Lifecycle-1783908324640](Assets/Data%20Engineering%20Lifecycle-1783908324640.webp)
## Generation
A _source system_ is the origin of the data used in the data engineering lifecycle.
>[!example] Transactional DB, IOT devices, flat files (CSV, XML), web services 

A data engineer:
+ [p] consumes data from a source system
+ [c] doesn't own or control the source system
### Key considerations
+ [I] How is data generated? (Application, IOT devices)
+ [I] Frequency of data generation (How many events/s, GB/hr ?)
+ [I] How is data persisted at source? (Long term, short term)
+ [I] How reliable is the data? (Rate of occurrence of nulls, lousy formatting)
+ [I] Frequency of errors or duplicates
+ [I] What is the schema, and how are changes communicated to downstream stakeholders?
+ [I] How frequently should data be pulled from the source?
+ [I] Will some data values arrive later than other data produced simultaneously?
+ [I] Is data stateless or stateful? If stateful, how are changes provided? (Periodic snapshots, CDC)
+ [I] Will reading data from a source impact its performance? 
## Ingestion
Bottlenecks in ingestion:
+ [c] Source systems might become unresponsive / provide poor quality data.
+ [c] Ingestion service breaks down
### Key considerations
+ [I] What are the use cases for this data? Can it be reused, instead of storing duplicate data?
+ [I] Is data being generated and ingested reliably? Is it available for use when needed?
+ [I] How frequently will the data be accessed?
+ [I] In what volume will the data, typically arrive?
+ [I] Where will the data be stored?
+ [I] What format is the data in? Does downstream storage and transformation support it?
+ [I] How is data ingested? (Pull vs Push, Streaming vs Batch) 
## Storage
Storage runs across the entire data engineering lifecycle, often occurring in multiple places in a data pipeline, with storage systems crossing over with source systems, ingestion, transformation, and serving.
>[!example]-
>+ Cloud data warehouses can store, process and serve data for analysis.
>+ Streaming frameworks like Apache Kafka can function simultaneously as ingestion, storage, and query systems for messages, with object storage being a standard layer for data transmission
### Key considerations
+ [I] Is the storage compatible with the R/W speed of the pipeline?
+ [I] Are you utilizing the storage optimally? (Random access updates in object storage is an antipattern)
+ [I] Can it handle anticipated future scale? (total storage space, read operation rate, write volume)
+ [I] Capturing metadata (schema evolution, data flows, lineage) and managing governance (MDM or golden records + compliance)
+ [I] Is this a pure storage solution (object storage), or does it support complex query patterns (i.e., a cloud data warehouse)?
+ [I] Is the storage system schema-agnostic (object storage)? Flexible schema (Cassandra)? Enforced schema (a cloud data warehouse)?
+ [I] How often is the data accessed?
### Data access frequency
__Hot data__: Data that is accessed multiple times a day or second. (Frequent access)
__Lukewarm data__: Data is accessed every week or month (Infrequent access)
__Cold data__: Data is accessed very rarely. (Archival access)
## Transformation
Basic transforms map data into correct data types (ex: string to numeric/date), standardizing formats, dropping bad data.
Advanced transforms map data to a schema and apply normalization/aggregation.
### Key considerations
+ [I] What is the cost and ROI of the transformation?
+ [I] Are they self-isolated and simple? 
	+ Makes maintenance and troubleshooting easier (A failure can be pinpointed instantly to a specific modular step).
	+ Isolation promotes Idempotent pipeline.
	+ Isolated transforms allow the workload to be distributed across multiple compute clusters
## Serving
