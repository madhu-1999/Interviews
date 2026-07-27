#spark #de 

# SparkContext
It is the __original entry point__ for Spark functionality, used by the driver to establish a connection to the cluster and resource manager to coordinate and execute jobs. It provides ==access to low-level operations and configurations==.[^1][^4]
==Essential for working directly with Resilient Distributed Datasets (RDDs)==.
```python
from pyspark import SparkContext, SparkConf
conf = SparkConf()
		.setAppName("Spark Introduction")
		
sc = SparkContext(conf=conf)
```

# SparkSession 
It provides a **single point of entry** to start and configure a Spark application.
It combines the old `SQLContext`, `HiveContext`, and `SparkContext` into a single point of entry.[^1][^4]
Use `SparkSession` when working with ==high-level APIs such as DataFrame and Dataset==.
```python
from pyspark.sql import SparkSession
spark = SparkSession
	.builder
	.appName("Spark Introduction")
	.config("spark.some.config.option", "value")
	.master("local[*]") 
	.enableHiveSupport() # Use if you need access to HiveContext
	.getOrCreate()
# The `master` method is used to set the master URL for the Spark Session. This determines where the Spark application will run.
# `local[*]` --> run Spark locally with, no of workers = no of logical cores in machine.

# Two ways to access SparkContext
sc = spark.sparkContext
sc = spark._sc
```

# Parallelizing non-distributed tasks (`parallelize()`)
The `parallelize()` method is used to take an existing collection in your driver program (like a Python list) and distribute it across the cluster to create a [[Spark - Introduction#RDDs|Resilient Distributed Dataset (RDD).]][^2]

Because `parallelize()` requires all data to fit in the memory of the local driver program first, it is **not used for processing massive production datasets**.

Spark splits the collection into _partitions_. You can manually specify the number of partitions as a second argument (e.g., `spark.parallelize(data, 10)`), or let Spark use a default value based on your cluster's CPU cores.

==The method itself is lazy==; the actual distribution of data only happens when an "action" (like `count()` or `collect()`) is called on the resulting RDD.
## Scenario: Unit Testing or Prototyping
```python
# Assuming SparkSession object `spark` created
test_data = ["apple banana", "banana cherry", "apple cherry blueberry"]

# Distribute it to simulate a cluster environment
input_rdd = spark.sparkcontext.parallelize(test_data)

# Test your transformation logic
word_counts = input_rdd.flatMap(lambda line: line.split(" ")) \
                       .map(lambda word: (word, 1)) \
                       .reduceByKey(lambda a, b: a + b)

print(word_counts.collect())
# Output: [('apple', 2), ('banana', 2), ('cherry', 2), ('blueberry', 1)]
```
## Scenario: Distributing Seed Data or Parametric Grids (Hyperparameter Tuning)
You want to run an expensive algorithm, or simulation in parallel across multiple machines. You define a list of configurations, seeds, or parameters on the driver node, parallelize that list, and let each worker handle a different parameter set.
```python
import time
# Assuming SparkSession object `spark` created
param_grid = [
    {"learning_rate": 0.01, "epochs": 10},
    {"learning_rate": 0.05, "epochs": 20},
    {"learning_rate": 0.1,  "epochs": 10},
    {"learning_rate": 0.2,  "epochs": 50}
]

# Parallelize the configurations so workers can process them concurrently
param_rdd = spark.sparkContext.parallelize(param_grid, 4)

# Define a function to train a model locally on a worker using the parameters
def train_model_dummy(params):
    # Simulate an expensive training process
    time.sleep(2) 
    accuracy = (params["learning_rate"] * 10) + (params["epochs"] * 0.01)
    return {**params, "accuracy": min(accuracy, 1.0)}

# Run the training concurrently across the cluster
results = param_rdd.map(train_model_dummy).collect()
print(results)

# O/P:
# [
	# {'learning_rate': 0.01, 'epochs': 10, 'accuracy': 0.2}, 
	# {'learning_rate': 0.05, 'epochs': 20, 'accuracy': 0.7}, 
	# {'learning_rate': 0.1, 'epochs': 10, 'accuracy': 1.0}, 
	# {'learning_rate': 0.2, 'epochs': 50, 'accuracy': 1.0}
# ]
```
## Scenario: Parallelizing Batch API Fetching
You have a list of 100 specific product URLs or API endpoints that you need to scrape or query. Instead of iterating through them sequentially on your local machine (which would take hours), you parallelize the list of target strings so your Spark executors can make concurrent network requests.
```python
import requests
# Assuming SparkSession object `spark` created
# Small array of target endpoints sitting on the driver program
api_endpoints = [
    "https://api.example.com/v1/status",
    "https://api.example.com/v2/status",
    "https://api.example.com/v3/status"
]

# Distribute the target strings to the cluster workers
urls_rdd = spark.sparkContext.parallelize(api_endpoints)

# Function executed by workers to fetch web data in parallel
def fetch_data(url):
    try:
        response = requests.get(url, timeout=5)
        return (url, response.status_code)
    except Exception as e:
        return (url, "Failed")

# Fetch all endpoints concurrently
network_results = urls_rdd.map(fetch_data).collect()
print(network_results)
```
# Creating DataFrame
>[!important] Schema inference introduces an additional stage in the Spark job execution plan

>[!warning]- Inferring schema is slower than custom schema 
>This is because Spark looks at all the data in the column before assigning a type. We can choose to look at only a subset of data in a column for inference, but this is prone to errors, since it is possible that the column is a mixed type but the subset that you happened to look at was filled with numbers.
## From a list of data
```python
employee_data = [
	("101", "Alice", "HR", "USA", 70000),
	("102", "Bob", "Engineering", "UK", 85000),
	("103", "Charlie", "Finance", "Canada", 65000)
]

# Define column headers 
columns = ["Department_ID", "Employee_Name", "Department", "Country", "Salary"] 

emp_df = spark.createDataFrame(data=employee_data, schema=columns)
 
# Display results 
emp_df.show() # Shows first 20 rows
emp_df.printSchema()
```
In this example, PySpark automatically inferred the datatypes of columns based on the values in the columns.
```
root
 |-- Department_ID: string (nullable = true)
 |-- Employee_Name: string (nullable = true)
 |-- Department: string (nullable = true)
 |-- Country: string (nullable = true)
 |-- Salary: long (nullable = true)
```
## Reading from Files
```python
# Spark infers schema
df = spark.read.format("csv") \
    .option("header", "true") \
    .option("inferSchema", "true") \
    .load("path_to_file.csv")
    
# Custom schema
custom_schema = "id INT, name STRING, entry_date DATE" 
df = spark.read.format("csv")  
	.schema(custom_schema) 
	.option("header", "true")
	.load("path/to/your/file.csv")
```
## Custom Schema
```python
from pyspark.sql.types import StructType, StructField, StringType, IntegerType 
 
# Define the schema  
schema = StructType([  
	StructField("Name", StringType(), False),  
	StructField("Age", IntegerType(), True)  
])  

# Create DataFrame with manually defined schema  
df = spark.createDataFrame(data, schema=schema)  

# Show DataFrame and schema  
df.show()  
df.printSchema()
# root  
# | — Name: string (nullable = false)  
# | — Age: integer (nullable = true)
```
## Converting from RDD
```python
rdd = spark.sparkContext.parallelize([("Swayam", 24), ("Sonali", 45), ("Sejal", 29)])
  
df_rdd = rdd.toDF(["Name", "Age"]) 
 
df_rdd.show()  
df_rdd.printSchema()

# root  
# | — Name: string (nullable = true)  
# | — Age: integer (nullable = true)
```
# Column Reference
## Column String
```python
df.select("Username", "Identifier")
```
## Column Object
A `Column` object represents a column in a DataFrame.  
It encapsulates various operations and transformations that can be performed on the data within that column.

# Multithreading with Spark
## Multithreading on the Driver (Concurrent Job Submission)
Let's take a simple example: We want to get the record count of all the tables in our data pipeline and write it into a control table.[^3]

The naive approach to get the count is running a for-loop to get the count like this and then write into the control table:
```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import current_date

# Initialize Spark Session
spark = (
SparkSession.builder
			.appName("MultithreadingExample")
			.getOrCreate()
)
# List of tables
TABLES = [
    "db1.table1",
    "db1.table2",
    "db2.table3",
    "db2.table4",
    "db3.table5",
    "db3.table6",
]

# List to keep the dictionary of table_name and respective count
table_count = []


# Function to get table records count
def get_count(table: str) -> dict:
    count_dict = {}
    count_dict["table"] = table
    try:
        # `count` triggers a job
        # Jobs execute sequentially since main driver thread blocks
        # and waits at `count` for executors to finish computing
        count = spark.read.table(table).count()
        count_dict["count"] = count
    except Exception:
        count_dict["count"] = 0
    return count_dict


if __name__ == "__main__":
    # Pure python code, executes in driver
    for table in TABLES:
        table_count.append(get_count(table))

    # Create dataframe from list
    count_df = spark.createDataFrame(table_count).withColumn("date", current_date())

    # writing into the table
     count_df.coalesce(1).write.insertInto("control_db.counts_table")
```
This will work as expected and give us the desired results, but this is not an efficient since, counts of tables are _independent_ of each other and jobs are executed _sequentially_.

Leveraging multithreading here, for concurrent job submission will improve _speed_ as well as _resource utilization_.
```python hl:44-46
from concurrent.futures import ThreadPoolExecutor
from pyspark.sql import SparkSession
from pyspark.sql.functions import current_date

# Initialize Spark Session
spark = (
    SparkSession.builder
			    .appName("MultithreadingExample")
			    .getOrCreate()
)

# List of tables
TABLES = [
    "db1.table1",
    "db1.table2",
    "db2.table3",
    "db2.table4",
    "db3.table5",
    "db3.table6",
]

# List to keep the dictionary of table_name and respective count
table_count = []


# Function to get table records count
def get_count(table: str) -> dict:
    count_dict = {}
    count_dict["table"] = table
    try:
        # `count` triggers a job
        # Jobs execute parallely since mueltipl driver threads block and wait till execution completes
        count = spark.read.table(table).count()
        count_dict["count"] = count
    except Exception:
        count_dict["count"] = 0
    return count_dict


if __name__ == "__main__":
    # Pure python code, executes in driver
    # Concurrent job submission
    # Each thread in the pool executes the `get_count` method for a <table> in `TABLES` concurrently
    with ThreadPoolExecutor(max_workers=6) as executor:
        table_count = executor.map(get_count, TABLES)

    # Create dataframe from list
    count_df = spark.createDataFrame(table_count).withColumn("date", current_date())

    # writing into the table
    count_df.coalesce(1).write.insertInto("control_db.counts_table")
```
## Multithreading on Executors
This approach is less common and requires care, but it is highly effective for specialized tasks. If a task needs to ==do something that wastes time waiting==—like calling an external REST API or querying a database row-by-row—the CPU core assigned to that task sits idle.
```python
import requests
from concurrent.futures import ThreadPoolExecutor

def fetch_api_data_parallel(partition_iterator):
    def call_api(row):
        # Simulating an external network call
        response = requests.get(f"https://api.example.com/data/{row.id}")
        return row.id, response.status_code

    # Process rows in the partition concurrently using threads
    with ThreadPoolExecutor(max_workers=5) as executor:
        results = list(executor.map(call_api, partition_iterator))
    
    return iter(results)

# Apply the threaded function across the distributed Spark partitions
rdd_results = df.rdd.mapPartitions(fetch_api_data_parallel)
```
By opening a thread pool _inside_ the Executor task, you can utilize that idle wait time to fire off multiple requests concurrently. But you must ==heavily restrict the number of threads per worker to avoid running out of memory (OOM)== or overwhelming the external system.
[^1]:    [SparkSession vs SparkContext](https://medium.com/@DataWithSantosh/understanding-sparksession-and-sparkcontext-in-pyspark-e74ecc688886)

[^2]: [Parallelize non distributed tasks](https://milescole.dev/data-engineering/2024/10/11/Parallelizing-Non-Distributed-Tasks.html)

[^3]: [Spark Multithreading](https://www.guptaakashdeep.com/enhancing-spark-job-performance-multithreading/)

[^4]: [Spark Contexts](https://towardsdatascience.com/sparksession-vs-sparkcontext-vs-sqlcontext-vs-hivecontext-741d50c9486a/)
