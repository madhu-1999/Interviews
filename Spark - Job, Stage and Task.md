---
tags:
  - "#de"
  - spark
  - distributed
---
# Job
[^1]
A job contains a __series of [transformations](Spark%20-%20Transformations.md)__, which is optimized into a logical plan by Spark.
	__One [action](Spark%20-%20Introduction.md#Actions) = one job !__

The logical plan is broken down into __stages__.
# Stage
[^1]
A stage contains a series of [narrow transformations](Spark%20-%20Transformations.md#Narrow%20transformations).
Every time a [wide transformation](Spark%20-%20Transformations.md#Wide%20Transformations) is encountered, a new stage is created. (Data is moved between stages via shuffle and sort operations.)
+ [i] If a logical plan has no wide transformations in it, there will be only one stage in the job.

For each stage, the driver divides the workload into multiple tasks.

>[!NOTE] Spark AQE and code optimizations were turned off to get the following DAGs.

```python
# --- JOB 0: Simple Action ---
# This triggers the first Job.
# 1 Stage since no wide transforms are applied.
print("--------- Job 0 ---------")
result = df.filter(F.col("salary") > 50000)
print(f"Filtered records: {result.collect()}")
```
![Spark - Job, Stage and Task-1785260373362](Assets/Spark%20-%20Job,%20Stage%20and%20Task-1785260373362.webp)
```python
# --- JOB 1: Narrow -> Wide -> Action ---
# 1. Narrow Transform: filter (stays in Stage 1)
# 2. Wide Transform: groupBy (triggers Stage 2 / Shuffle)
# 3. Action: collect (triggers the Job)
result = df.filter(F.col("salary") > 50000).groupBy("department").agg(F.avg("salary"))

# This triggers the second Job, which will have 2 Stages
print("--------- Job 1 ---------")
final_output = result.collect()
print(f"Avg salary by department: {final_output}")
```

![Spark - Job, Stage and Task-1785260619945](Assets/Spark%20-%20Job,%20Stage%20and%20Task-1785260619945.webp)
<center><b>Simplified Representation of Job 2</b></center>
![Apache Spark-1778698144623](Assets/Apache%20Spark-1778698144623.webp)
# Task
It is the __smallest unit of work__ in Spark.
	__One partition = one task__

The driver assigns tasks to executors, which process them in parallel. Balancing the number of tasks and partition sizes is essential to avoid performance bottlenecks.

>See [Spark - Partition](Spark%20-%20Partition) for more details.


[^1]: https://blog.dataengineerthings.org/deep-dive-into-spark-jobs-and-stages-481ecf1c9b62
