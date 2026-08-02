---
tags:
  - "#de"
  - spark
  - distributed
---
# RDD and Lineage
[^1][^2]
RDDs provide fault tolerance by maintaining the lineage of transformations applied to the data. In case of a failure, Spark can recompute the lost [partition](Spark%20-%20Partition.md) of an RDD by following the lineage, ensuring data recovery without the need to reprocess the entire dataset.

>[!faq]- What is lineage?
>It is a DAG that represents a sequence of transformations applied to a RDD from its source to its current state.
>Each node in the lineage graph represents a parent RDD, and each edge represents a transformation operation (e.g., map, filter) that was applied to derive the child RDD.
>![RDDs in Spark-1784475581781](Assets/RDDs%20in%20Spark-1784475581781.webp)
# Executor Failure
[^1]
If a worker node or executor fails, the driver will detect this failure and reschedule failed tasks to run on other available executors.

>[!faq]-  How does the driver know if a worker has failed?
>[^3]
> All executors send the driver a _periodic status message_ called the __heartbeat__, which contains task metrics.
> The __heartbeat__ is sent at a fixed interval specified by `spark.heartbeatInterval`.
> The driver checks that it has received these messages periodically. <mark>If an executor's last heartbeat is older than `spark.network.timeout` , it is considered dead.</mark>

# Driver Failure
[^1][^4]
If the driver fails, then `SparkSession` and all the executors with their in-memory data will be lost. On restarting the driver, a new `SparkSession` will be created from the [checkpoint](Spark%20-%20Persisting%20Data.md#Checkpointing) directory (if created) and resume processing. 

[^1]: https://medium.com/@omarlaraqui/how-apache-spark-is-fault-tolerant-89edfb27145b

[^2]: https://medium.com/@sujathamudadla1213/what-is-lineage-information-in-spark-202fff29ede3

[^3]: https://www.waitingforcode.com/apache-spark/spark-failure-detection-heartbeats/read

[^4]: https://stackoverflow.com/questions/72090912/what-if-driver-in-spark-job-fails
