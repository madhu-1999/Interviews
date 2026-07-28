---
tags:
  - "#spark"
  - "#python"
---
# Narrow Transformations
[^1]
## `filter()`

Return a new RDD containing only the elements that satisfy a predicate.

```python
rdd = sc.parallelize([1, 2, 3, 4, 5])

# O/P: [2, 4]
print(rdd.filter(lambda x: x % 2 == 0).collect())
```
## `flatMap()`

Returns an iterable per input and flattens those [iterables](Python%20-%20Iterable%20and%20Iterators.md), so one input can produce zero or many outputs.

```python
# --------- Filtering Data (1 to 0 Mapping) -----------
numbers_rdd = sc.parallelize([1, 2, 3, 4, 5])

# Keep only even numbers, discard odd numbers by returning an empty list
evens_rdd = numbers_rdd.flatMap(lambda x: [x] if x % 2 == 0 else [])
# O/P: [2, 4]
print(evens_rdd.collect())

# --------- Generating Combinations (1 to Many Mapping) -----------
base_rdd = sc.parallelize([2, 3])

# Generate a list range for each number
ranges_rdd = base_rdd.flatMap(lambda x: range(1, x))

# For 2: range(1, 2) -> [1]
# For 3: range(1, 3) -> [1, 2]
# O/P: [1, 1, 2]
print(ranges_rdd.collect())
```
## `flatMapValues()`

Applies to PairRDDs only. It transforms the value, flattens the resulting iterable, and pairs each flattened item with the original key without triggering a shuffle.

```python
rdd = sc.parallelize([("a", ["x", "y", "z"]), ("b", ["p", "r"])])
# Apply a function that returns the iterable value itself
result = rdd.flatMapValues(lambda x: x)
print(result.collect())
# O/P: [('a', 'x'), ('a', 'y'), ('a', 'z'), ('b', 'p'), ('b', 'r')]
```

>[!faq] `flatMap()` vs `flatMapValues()`
>
>|Feature|`flatMap`|`flatMapValues`|
|---|---|---|
|**Target Data**|Any RDD (individual elements or key-value tuples).|Pair RDDs only (explicit key-value structure).|
|**Function Scope**|Applies to the entire tuple `(K, V)`.|Applies **only to the value** `V`.|
|**Key Modification**|Can change, drop, or generate new keys.|**Cannot change** the original keys.|
|**Output Structure**|A flat collection of standalone elements.|A flat collection of `(original_key, new_value)` pairs.|
|**Partitioning**|Destroys the original RDD partitioning.|**Retains** the original RDD partitioning.|
## `map()`
## `mapPartitions()`
## `mapPartitionsWithIndex()`
## `mapValues()`
## `union()`

# Wide Transformations
[^1]
## `aggregateByKey()` 

[^4]
It takes a Pair [RDD](Spark%20-%20RDD.md) (RDD of key-value pairs) and aggregates values of each key, producing a new Pair RDD with aggregated values per key.
	The input type $V$ can be different from the output type $C$.
```python
rdd = sc.parallelize([("a", 1), ("b", 2), ("a", 3)])
aggregated_rdd = rdd.aggregateByKey(
    zeroValue=0,
    seqFunc=lambda acc, val: acc + val,
    combFunc=lambda acc1, acc2: acc1 + acc2,
)
result = aggregated_rdd.collect()
print(result)  # Output: [('a', 4), ('b', 2)]
```

![Spark - RDD Operations Reference-1785056140668](Assets/Spark%20-%20RDD%20Operations%20Reference-1785056140668.webp)
+ [i] `zeroValue` is the initial value for the accumulated result of each partition. It is the ==same for every key in every partition==
+ [i] `seqFunc` aggregates values within each partition by combining the current aggregate (starting from `zeroValue`) with each value for a key. 
+ [i] `combFunc` merges, aggregates from different partitions for a key after shuffling.

>[!tip] It is faster than `groupByKey()` because, it first performs a local aggregation (`seqFunc`) before shuffling, reducing overhead.

>[!info] `aggregateByKey()` preserves the partitioning of the input RDD, unless a custom partitioner is specified.
## `combineByKey()`

[^6]
It takes a Pair [RDD](Spark%20-%20RDD.md) (RDD of key-value pairs) and aggregates values of each key, producing a new Pair RDD with aggregated values per key.
	The input type $V$ can be different from the output type $C$.

```python
rdd = sc.parallelize([("alice", 10), ("alice", 20), ("bob", 50), ("alice", 5)])

# createCombiner receives the first value directly
# State format: (first_value, total_sum, count)
createCombiner = lambda val: (val, val, 1)

# mergeValue adds subsequent values to the running sum and count
mergeValue = lambda acc, val: (acc[0], acc[1] + val, acc[2] + 1)

# mergeCombiners combines intermediate states across partitions
mergeCombiners = lambda acc1, acc2: (
    acc1[0],  # Keep original first value
    acc1[1] + acc2[1],
    acc1[2] + acc2[2],
)

result = rdd.combineByKey(createCombiner, mergeValue, mergeCombiners)
# Result: [('alice', (10, 35, 3)), ('bob', (50, 50, 1))]
print(result.collect())
```

It takes three mandatory arguments:
+ [i] `createCombiner`: A function to turn $V$ into $C$ i.e. we define the output shape and the ==initial value can be different for each key in the partition==.
+ [i] `mergeValue` : A function that defines behavior, when a key is encountered again within the same partition.
+ [i] `mergeCombiners`: A function that merges accumulators produced by different partitions for the same key. 

>[!info] `aggregateByKey()`, `foldByKey()` use `combineByKey()` internally!

>[!info] `combineByKey()` preserves the partitioning of the input RDD, unless a custom partitioner is specified.
## `distinct()`

Returns a RDD containing only unique values.
```python
words = ["cat", "dog", "cat", "elephant", "dog"]
words_rdd = sc.parallelize(words, 3)
print(words_rdd.distinct().collect())
```

>[!info] It does not preserve partitioning information
## `foldByKey()`

It takes a PairRDD (RDD of key-value pairs) and aggregates values of each key, producing a new Pair RDD with aggregated values per key.
	==The input type must be the same as the output type==.

```python
rdd = sc.parallelize([("a", 1), ("b", 1), ("a", 1)])
# O/P: [('a', 2), ('b', 1)]
print(rdd.foldByKey(0, lambda x, y: x + y).collect())
```
+ [i] `zeroValue` is the initial value for the accumulated result of each partition.
+ [i] `op` is the aggregation func, that is used to merge values within a partition and to merge partitions.   
>[!info] `foldByKey()` preserves the partitioning of the input RDD, unless a custom partitioner is specified. 
## `groupBy()`
## `groupByKey()`
## `join()`
## `sortBy()`
## `sortByKey()`

# Actions
## `aggregate()`

[^2][^3]

>[!tip] `aggregate()` is faster than using a combination of `map()` and `reduce()`.

```python
rdd = sc.parallelize([1, 2, 3, 4, 5, 6, 7])
res = rdd.aggregate(
    zeroValue=(0, 0),
    # This function combines within partition
    seqOp=lambda acc, num: (acc[0] + num, acc[1] + 1),  # type: ignore
    # This function combines partitions
    combOp=lambda partition_1, partition_2: (
        partition_1[0] + partition_2[0],
        partition_1[1] + partition_2[1],
    ),  # type: ignore
)

# O/P: 4.0
print(f"Average: {res[0] / res[1]}")
```

+ [i] `zeroValue` is the initial value for the accumulated result of each partition for the `seqOp` operator, and also the initial value for the combine results from different partitions for the `combOp` operator.
+ [i] `seqOp` is an operator used to accumulate results within a partition.
+ [i] `combOp` is an associative operator used to combine results from different partitions. 
![Spark - RDD Operations Reference-1785031212619](Assets/Spark%20-%20RDD%20Operations%20Reference-1785031212619.webp)

>[!warning] All partitions send their reduced value to the driver. This becomes a bottleneck when there are many partitions and the data from each partition is big.
>>[!tip] Use `treeAggregate()` instead, it combines partitions on a small set of executors first before sending sending them to the driver.
## `collect()` 

Return a list that contains all the elements in this RDD.

```python
# O/P: [1, 2, 3, 4]
print(sc.parallelize([1, 2, 3, 4]).collect())
```

>[!warning] `collect()` should only be used if the resulting array is expected to be small, as all the data is loaded into the driver’s memory.
## `collectAsMap()`

Return key-value pairs in the RDD as a dictionary.

```python
# O/P: {1: 2, 3: 4}
print(sc.parallelize([(1, 2), (3, 4)]).collectAsMap())
```

>[!warning] `collectAsMap()` should only be used if the resulting data is expected to be small, as all the data is loaded into the driver’s memory.
## `count()`

Return the number of elements in this RDD.

```python
# O/P: 2
print(sc.parallelize([(1, 2), (3, 4)]).count())
```
## `countApprox()`

Returns an incomplete or estimated row count for an RDD within a strict time limit.

```python
rdd = sc.range(0, 1_000_000_000, numSlices=4)
print(rdd.countApprox(1000, confidence=0.90))
```
## `countApproxDistinct()` 

Return approximate number of distinct elements in the RDD, using _HyperLogLog_.
	`relativeSD`: Optional parameter that controls accuracy trade off when computing the number of distinct elements. ($\downarrow$ value $\implies$ $\uparrow$ accuracy).

```python
# O/P: 962 (Will vary)
print(sc.parallelize(range(1000)).map(str).countApproxDistinct())
```
## `countByKey()` 

Counts the number of values associated with each key in a Pair RDD and returns the result as a dictionary.

![Spark - RDD Operations Reference-1785092337224](Assets/Spark%20-%20RDD%20Operations%20Reference-1785092337224.webp)

>[!warning] All partitions send their reduced value to the driver. This becomes a bottleneck when there are many partitions and the data from each partition is big.
## `countByValue()`

[^5]
Return the count of each unique value in the RDD as a dictionary of (value, count) pairs.
	This operation is used with single-value RDDs, and ___NOT___ PairRDDs.

```python
rdd = sc.parallelize([1, 2, 1, 2, 2], 3)
# O/P: {1: 2, 2: 3}
print(rdd.countByValue())
```

![Spark - RDD Operations Reference-1785093751138](Assets/Spark%20-%20RDD%20Operations%20Reference-1785093751138.webp)

>[!warning] All partitions send their reduced value to the driver. This becomes a bottleneck when there are many partitions and the data from each partition is big.

## `first()`

Return the first element in this RDD.

```python
# O/P: 2
print(sc.parallelize([2, 3, 4]).first())

# O/P: ValueError: : RDD is empty
print(sc.parallelize([]).first())
```
## `fold()`

[^7]
Aggregates the elements in each partition, using a _initial_ value (`zeroValue`) and an aggregation function (`op`). 
	The provided `zeroValue` is used if RDD is empty or as the starting point of the aggregation.
	The aggregation function merges values _within_ a partition as well as merges partitions.
```python
rdd = sc.parallelize([1, 2, 3, 4, 5])

# Use fold to calculate the sum with an initial value of 0
total_sum = rdd.fold(0, lambda x, y: x + y)

# O/P: 15
print(total_sum)
```
## `foreach()`

Applies a function to all elements of this RDD. It returns nothing.

```python
def f(x): print(x)

sc.parallelize([1, 2, 3]).foreach(f)
# O/P: 
# 1
# 2
# 3
```
## `getNumPartitions()`

Returns the number of partitions in RDD.

```python
rdd = sc.parallelize([1, 2, 3, 4], 2)
# O/P: 2
print(rdd.getNumPartitions())
```
## `glom()`
## `reduce()`
## `reduceByKey()`
## `reduceByKeyLocally()`
## `take()`
## `takeOrdered()`
## `treeAggregate()`
## `treeReduce()`

[^1]: https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.RDD.html#pyspark.RDD

[^2]: https://blog.benbenbang.in/understanding-aggregation-method-in-spark-f2361c56a570

[^3]: https://umbertogriffo.gitbook.io/apache-spark-best-practices-and-tuning/rdd/treereduce_and_treeaggregate_demystified

[^4]: https://www.sparkcodehub.com/pyspark/rdd/aggregatebykey

[^5]: https://www.linkedin.com/posts/shrutika-suryawanshi-b9b631157_reducebykey-countbyvalue-activity-7323950052098342912-44td/

[^6]: https://medium.com/@sujathamudadla1213/difference-between-combinebykey-and-aggregatebykey-in-apache-spark-e9bd29e5a2d9

[^7]: https://medium.com/@sujathamudadla1213/explain-the-fold-action-and-its-differences-from-reduce-1b343f259542
