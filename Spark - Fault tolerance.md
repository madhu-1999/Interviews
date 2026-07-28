---
tags:
  - "#de"
  - spark
  - distributed
---
# RDD and Lineage
[^1][^2]
RDDs provide fault tolerance by maintaining the lineage of transformations applied to the data. In case of a failure, Spark can recompute the lost partition of an RDD by following the lineage, ensuring data recovery without the need to reprocess the entire dataset.

>[!faq]- What is lineage?
>It is a DAG that represents a sequence of transformations applied to a RDD from its source to its current state.
>Each node in the lineage graph represents a parent RDD, and each edge represents a transformation operation (e.g., map, filter) that was applied to derive the child RDD.
>![[RDDs in Spark-1784475581781.webp]]
# Executor Failure
[^1]

# Driver Failure

[^1]: https://medium.com/@omarlaraqui/how-apache-spark-is-fault-tolerant-89edfb27145b

[^2]: https://medium.com/@sujathamudadla1213/what-is-lineage-information-in-spark-202fff29ede3
