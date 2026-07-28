---
tags:
  - "#de"
  - "#spark"
  - "#distributed"
---
# Overview
[^1][^5]
**Resilient:** Fault-tolerant and capable of rebuilding data on failure.  
**Distributed:** Data distributed among multiple nodes in a cluster.  
**Dataset:** Represents records of the data you work with.

An `RDD` is an __in-memory__ data structure that is distributed across many servers within a Spark cluster.
It logically partitions data and the worker nodes store their segments of the partitioned RDD _in-memory_ for fast computation.
![Pasted image 20260430095939](Assets/Pasted%20image%2020260430095939.webp)
>[!info]- Rule of thumb: target 128MB–256MB per partition.
>$$\frac{data\_size\_gb * 1000 }{target\_mb\_per\_partition} = \text{ideal partition count}$$
>For 100 GB data:
>$$\frac{100 * 1000}{200} = 500\space\text{partitions}$$

![Pasted image 20260430094210](Assets/Pasted%20image%2020260430094210.webp)
# Features
## Immutable
[^4]
Once an RDD is created, it cannot be changed. Any modifications result in the creation of a brand-new RDD.

>[!faq]- Why are RDDs immutable?
> + [I] **Simpler caching**: Spark achieves high performance by keeping data in memory. If data were mutable, managing cache invalidation would be incredibly difficult. Immutability guarantees that once data is cached, it remains valid forever without the risk of stale reads.
> + [I] **Concurrency Without Locks**: In distributed systems, mutable data requires complex locking and synchronization mechanisms to avoid corruption. Since RDDs are immutable, race conditions are eliminated.
> + [I] **Fault tolerance**: It is achieved by replaying a part of the lineage to reconstruct lost data. If data was mutable, we could get different results on each replay.

>[!error]- Tradeoffs of immutability
>+ [c] **Memory pressure**: If you cache intermediate RDDs, each one occupies memory across the cluster.
>+ [c] **Recomputation Cost:** If intermediate RDDs are not explicitly cached or persisted, Spark will recompute parts of the lineage graph multiple times.
>+ [c] **GC overhead.** More objects, more churn, more work for the JVM. 
## **In-memory computation**
The data inside an RDD (partition of it) is stored by the executor in memory, temporarily to speed up computation.

> [!faq]- How does keeping data in memory (RAM) speed up data processing?
> + Disk I/O overhead is eliminated.
> + The data can be persisted in memory using `.cache()` and `.persist()`, which means, it can be reused every time we perform a given sequence of operations, instead of computing or fetching it, again and again.
> + Reading from disk requires constant serialization / deserialization. This overhead is also eliminated by keeping data in memory.

> See [Spark - Persisting Data](Spark%20-%20Persisting%20Data.md) for more details on `.cache()` and `.persist()`.
## Lazy Evaluation
[^2]
[Transformations](Spark%20-%20Transformations.md) are not executed immediately. Instead, they are recorded in the lineage and executed together when an [](Spark%20-%20Introduction.md#Actions|action) is called on it.
>[!faq]- What is lineage?
>It is a DAG that represents a sequence of transformations applied to a RDD from its source to its current state.
>Each node in the lineage graph represents a parent RDD, and each edge represents a transformation operation (e.g., map, filter) that was applied to derive the child RDD.
>![RDDs in Spark-1784475581781](Assets/RDDs%20in%20Spark-1784475581781.webp)
## Fault tolerance
[^3]
RDDs provide fault tolerance through lineage.If a failure occurs during any transformation or if an RDD is lost, Spark can recompute the lost partition or RDD by following the lineage. It only re-executes the missing or affected transformation, avoiding unnecessary recomputation.

>See [Spark - Fault tolerance](Spark%20-%20Fault%20tolerance.md) to understand other types of fault tolerance in Spark.
## Type Safety
RDDs provide compile time safety, but there is no schema enforcement.
RDDs also provide **no query or execution optimizations**.
This is because RDD treats data as a collection of raw objects (objects can be Java, Python or Scala objects), rather than tabular rows.
> [!warning] In PySpark, RDDs are essentially untyped, since compile time safety is only enforced if using a linter.
# Creating RDDs
[^1] 
## Parallelizing a collection
```python
employees = [(101, "Anu"), (102, "Bala"), (103, "Charan")]
employee_rdd = sc.parallelize(employees, 2)

# O/P:  [(101, 'Anu'), (102, 'Bala'), (103, 'Charan')]
print(employee_rdd.collect())
# O/P: [101, 102, 103]
print(employee_rdd.keys().collect())
# O/P:['Anu', 'Bala', 'Charan']
print(employee_rdd.values().collect())
```
## From another RDD
```python
# Existing RDD
original_rdd = sc.parallelize([1, 2, 3, 4, 5])

# Create a new RDD by multiplying each element by 2
new_rdd = original_rdd.map(lambda x: x * 2)

print(new_rdd.collect())
# Output: [2, 4, 6, 8, 10]
```
## From an external data source (HDFS / S3 / Azure Blob)
```python
# Setup: Reading a folder containing two files: 'file1.txt' and 'file2.txt'
path = "hdfs://namenode:9000/my_folder/"

# 1. Using textFile() -> Reads line-by-line
text_rdd = sc.textFile(path)
print(text_rdd.collect())
# Output: ['Line 1 from file 1', 'Line 2 from file 1', 'Line 1 from file 2']

# 2. Using wholeTextFiles() -> Reads file-by-file as (file_path, file_content)
whole_rdd = sc.wholeTextFiles(path)
print(whole_rdd.collect())
# Output: [
#   ('hdfs://.../file1.txt', 'Line 1 from file 1\nLine 2 from file 1'), 
#   ('hdfs://.../file2.txt', 'Line 1 from file 2')
# ]
```

> See [Spark - Reading & Writing Files](Spark%20-%20Reading%20&%20Writing%20Files.md) for more details
## From a DataFrame / Dataset
```python
# Create a sample DataFrame
df = spark.createDataFrame([(101, "Anu"), (102, "Bala")], ["id", "name"])

# Convert DataFrame to RDD
df_rdd = df.rdd

print(df_rdd.collect())
# Output: [Row(id=101, name='Anu'), Row(id=102, name='Bala')]
```
## Empty RDD
```python
empty_rdd = sc.range(0, 0)
# or
empty_rdd = sc.emptyRDD()
# O/P: []
print(empty_rdd.collect())
# O/P: True
print(empty_rdd.isEmpty())
```

# Anatomy of an RDD
[^6][^1]

| **Operation**              | **Meaning**                                                                            |
| -------------------------- | -------------------------------------------------------------------------------------- |
| `partitions()`             | A list of splits that divide the total dataset.                                        |
| `preferredLocations(p)`    | List nodes where partition $p$ can be accessed faster due to data locality. (Optional) |
| `dependencies()`           | A list of references to parent RDDs (lineage)                                          |
| `iterator(p, parentIters)` | A method to compute each partition/split, given iterators of  parent dependencies      |
| `partitioner()`            | Return metadata specifying whether the RDD is hash/range partitioned. (Optional)       |
For example, an RDD representing an HDFS file has a partition for each block of the file and knows which machines each block is on. Meanwhile, the result of a map on this RDD has the same partitions, but applies the map function to the parent’s data when computing its elements.
# When to use RDDs
+ __Data is unstructured__: Unstructured data sources such as media or text streams benefit from the performance advantages RDDs offer.
+ **Schema is unimportant**: Since RDDs do not impose schemas, use them when accessing specific data by column or attribute is not relevant.
+ **Transformations are low-level**: When you want more granular control over transforms and configurations.
+ **Optimization not necessary**: Use if you can forego the query and execution optimizations offered by DataFrames/Datasets.
[^1]: https://www.linkedin.com/pulse/anatomy-apache-sparks-rdd-deepak-rajak/

[^2]: https://medium.com/@sujathamudadla1213/what-is-lineage-information-in-spark-202fff29ede3

[^3]: https://medium.com/@omarlaraqui/how-apache-spark-is-fault-tolerant-89edfb27145b

[^4]: https://luminousmen.com/post/why-apache-spark-rdd-is-immutable/

[^5]: https://medium.com/@RRamya02/apache-spark-resilient-distributed-dataset-rdd-8718ff096005

[^6]: https://people.eecs.berkeley.edu/~matei/papers/2012/nsdi_spark.pdf
