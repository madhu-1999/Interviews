---
tags:
  - "#de"
  - "#spark"
  - "#distributed"
---
# Transformations
They are operations that transform your existing RDD or DataFrame into a new RDD or DataFrame.
+ [I] **Lazy evaluation**: They don't execute immediately.
+ [I] **Coarse-grained**: All transformations apply to the entire RDD/DataFrame/Dataset, and not on an individual record in it. 
+ [*] `map`, `filter`, `groupBy`, `flatMap`, `distinct`, `union`, `join` etc...
## Narrow transformations
They are operations, where each partition of the output DataFrame depends on at most one [partition](Spark%20-%20Partition.md) of the parent DataFrame. In simpler terms, the data required to compute an output record comes from only one input partition.
**Key characteristics of narrow transformations:**
- [i] They don't require data from other partitions   
- [p] They can be executed independently on each partition    
- [p] Data doesn't need to be shuffled across the cluster    
+ [*]  `select()`, `selectExpr()`, `filter()`, `where()`, `withColumn()`, `drop()`, `union()`, `sample()`, `explode()`, `load()` etc...
![Apache Spark-1778544164515](Assets/Apache%20Spark-1778544164515.webp)
The image shows how the state of each partition changes before the transformation is applied.
## Wide Transformations
Wide transformations, on the other hand, are operations where each partition of the output may depend on data from multiple partitions of the input. This means data from different partitions needs to be combined.
**Key characteristics of wide transformations:**
- [i]   They require data from multiple partitions
- [c]  They involve [data shuffling](#Data%20shuffling) across the cluster
- [i] They create [stage boundaries](Spark%20-%20Job,%20Stage%20and%20Task.md#Stage) in the execution plan
- [*] Examples include: `groupBy()`, `agg()`, `join()`, `orderBy()`, `sort()`, `distinct()`, `pivot()`, and operations that use `window` functions.
![Apache Spark-1778543527311](Assets/Apache%20Spark-1778543527311.webp)
The image shows how the state of each partition changes before the transformation is applied.
# Transformations vs Actions
| Feature            | Transformations                                                           | Actions                                                                     |
| ------------------ | ------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| **Nature**         | Lazy (deferred computation)                                               | Eager (immediate computation)                                               |
| **Execution**      | Builds a lineage of operations; doesn’t execute until an action is called | Triggers the execution of the entire lineage                                |
| **Return Value**   | Returns a new RDD or DataFrame                                            | Returns a value to the driver program (e.g., count, collected data, etc.)   |
| **Effect on Data** | Creates a new dataset; original dataset remains unchanged                 | May modify data (e.g., writing to a file) but primarily retrieves results   |
| **Memory Usage**   | Generally lower memory usage until an action is triggered                 | Can consume significant memory, especially with `collect` on large datasets |
# Further Reading
[Spark - RDD Operations Reference](Spark%20-%20RDD%20Operations%20Reference.md)