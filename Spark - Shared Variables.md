---
tags:
  - "#de"
  - "#spark"
  - "#distributed"
---
# Broadcast Variables
[^1][^2]
They allow us to **efficiently share a small, read-only dataset** to all executors in a cluster.
Instead of sending the data with each task, it is sent once from the driver to each executor, minimizing network I/O.
![Spark - Shared Variables-1784734785613](Assets/Spark%20-%20Shared%20Variables-1784734785613.webp)
## Example
```python
# Use case: Mapping country code to country name
# Sample transactions
transactions = [(1, 500, "US"), (2, 300, "IN"), (3, 700, "IN"), (4, 450, "US")]
columns = ["customer_id", "amount", "country_code"]
df = spark.createDataFrame(transactions, columns)

# lookup dict
country_dict = {
	"US": "United States", 
	"IN": "India", 
	"UK": "United Kingdom"
}

# Broadcast the lookup dict
broadcast_country = spark.sparkContext.broadcast(country_dict)

# Convert mapping dict to pyspark map expr
country_map = F.create_map([F.lit(x) for x in chain(*country_dict.items())])

# Map country code to country name
df_enriched = df.withColumn(
    "country_name",
    F.coalesce(country_map[F.col("country_code")], F.lit("Unknown")),
)

df_enriched.show()
spark.stop()

""" Output
+-----------+------+------------+-------------+
|customer_id|amount|country_code| country_name|
+-----------+------+------------+-------------+
|          1|   500|          US|United States|
|          2|   300|          IN|        India|
|          3|   700|          IN|        India|
|          4|   450|          US|United States|
+-----------+------+------------+-------------+
"""
```
## Removing data
>[!warning] Methods are non-blocking. Set `blocking=True` to block, until resources are freed.

To release the resources that the broadcast variable copied onto executors, call `.unpersist()`.
	If the broadcast is used again afterwards, it will be re-broadcast.

To permanently release all resources used by the broadcast variable, call `.destroy()`.
	The broadcast variable can’t be used after this.
## When to use?
+ [I] When tasks across multiple stages need the same data.
+ [I] When caching the data in **deserialized** form is important.
# Accumulators
[^1][^3]
It is a shared variable used to **calculate simple global aggregations** across all executor nodes in a cluster.
+ [i] Read-only for the driver
+ [i] Write-only for the executors 
+ [i] Supports commutative and associative operations 
+ [!] Use with [actions](Spark%20-%20Introduction.md#Actions), ***NOT*** [transformations](Spark%20-%20Transformations.md).

>[!faq] Why can't global variables be used for global aggregations?

[^4]
```python
# Use case: Sum of all variables
sc = spark.sparkContext
counter = 0

# Create rdd with 2 partitions
rdd = sc.range(0, 10, numSlices=2)


def increment(x):
    global counter
    counter += x


mapped_rdd = rdd.foreach(increment)

# O/P: Counter value: 0
print(f"Counter value: {counter}")
spark.stop()
```

The `counter` variable never updates! This is because, when the driver creates tasks, it attaches the task's closure to it. i.e. each executor gets its own copy of the methods and variables.
Since, each executor has it's own copy of the `counter` variable, it updates its _local_ copy, and not the _global_ `counter` variable.

>[!faq] Why can't accumulators be used with transformations?

[^5]
Spark can rerun a task in a few instances:
1. When a task encounters an exception, Spark will re-execute the task 4 times by default.
2. If an executor crashes, Spark will re execute the tasks 
3. If a task is running slow, Spark can rerun another copy of the task and this is called speculative execution. It only takes results from the task which completes first.

When a task re-executes, it will execute all the transformation functions in the task and this causes the accumulator value which was already manipulated by the first execution of the task to get manipulated again causing duplication in the accumulator’s result.
## Example
```python
sc = spark.sparkContext
'''sc.accumulator() takes 2 params:
		value: Initial value
		accum_param: object of Accumulator type to use (default is `LongAccumulator`)
'''
counter = sc.accumulator(0)

# Create rdd with 2 partitions
rdd = sc.range(0, 10, numSlices=2)

mapped_rdd = rdd.foreach(lambda x: counter.add(x))

# O/P: Counter value: 45
# (0 + 1 + 2 + 3 + 4... + 9) = 45
print(f"Counter value: {counter.value}")
spark.stop()
```
## Numeric Accumulators
[^6]
These are the most common type of accumulators and support numeric types like:
- `LongAccumulator` (default and most used): `sc.longAccumulator()` 
- `DoubleAccumulator`: `sc.doubleAccumulator()` 
## Custom Accumulators
[^6][^7]
Custom accumulators must subclass `AccumulatorParam` and provide implementation for 
+ `zero()`: Defines zero value for the type and 
+ `addInPlace()`: Defines how to add two values.

```python
# Custom accumulator
class ListAccumulatorParam(AccumulatorParam):
    def zero(self, value: Any) -> Any:
        # Return a zero value of your type 
        return []

    def addInPlace(self, value1: Any, value2: Any) -> Any:
	    # Define how to combine two values 
        value1.extend(value2)
        return value1

sc = spark.sparkContext

# Create a list accumulator
list_accum = sc.accumulator([], ListAccumulatorParam())


# Accumulate all the even numbers
def process_data(x):
    global list_accum
    if x % 2 == 0:
        list_accum += [x]


rdd = sc.range(1, 10, numSlices=2)
rdd.foreach(process_data)

# O/P: [2, 4, 6, 8]
print(list_accum.value)
spark.stop()
```
## When to use?
[^6]
- [I] **Monitoring application state or progress**: Keeping count of events like count of processed records, skipped rows, null values, or parsing errors.
- [I] **Debugging**: Track specific events, flag unusual conditions, or gather diagnostic information from within your distributed tasks.
- [I] **Custom metrics**:  Accumulators allow you to implement custom aggregations that may not be possible using built-in aggregation functions in a fault-tolerant manner.
## Limitations
[^7]
+ [c] **Not Thread-Safe for Driver Operations:** Accumulators are not designed for concurrent writes in the driver.
+ [c] **No Granular Control:** You can’t access intermediate values during execution.
+ [c] **Limited Use Cases:** Use accumulators for simple aggregations. For complex aggregations, it is best to use a distributed data structure suited for the task.

[^1]: https://blog.devgenius.io/broadcasting-variables-and-accumulators-in-pyspark-a94cd80720d0

[^2]: https://spark.apache.org/docs/latest/rdd-programming-guide.html#broadcast-variables

[^3]: https://spark.apache.org/docs/latest/rdd-programming-guide.html#accumulators

[^4]: https://spark.apache.org/docs/latest/rdd-programming-guide.html#understanding-closures

[^5]: https://www.bigdatainrealworld.com/what-are-accumulators-in-spark-when-and-when-not-to-use-them/

[^6]: https://medium.com/art-of-data-engineering/accumulators-in-apache-spark-the-interview-question-you-need-to-know-a02ea1f398d9

[^7]: https://medium.com/@kiranvutukuri/accumulators-in-apache-spark-part-51cbc7d0bc65
