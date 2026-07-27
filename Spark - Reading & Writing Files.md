---
tags:
  - "#de"
  - spark
  - distributed
---
# [[Spark - RDD|RDD]]
[^2]
## Reading Files
[^1][^3][^4]
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

Both `.textFile()` and `.wholeTextFiles()` are evaluated lazily i.e. they are executed only when an action is called.

| Feature           | `sc.textFile()`                                             | `sc.wholeTextFiles()`                                                  |
| ----------------- | ----------------------------------------------------------- | ---------------------------------------------------------------------- |
| **Return Type**   | `RDD` (Line-based)                                          | `PairRDD` (Tuple (file path, line))                                    |
| **Best Used For** | Large individual files or logs                              | Directories with many small files                                      |
| **Memory Risk**   | **Low**. Streams or splits large files into chunks of 32 MB | **High**. Loads whole files into memory. (No partial spilling to disk) |
| **Line order**    | Not preserved                                               | Preserved                                                              |
## Writing Files
[^5]
```python
rdd = spark.sparkContext.parallelize(['Hello', 'world', 'PySpark'])  

# Save RDD as TextFile, where each element is converted to a string and written as a separate line.
rdd.saveAsTextFile("/content/sample_data/output_text_file.txt")

rdd = spark.sparkContext.parallelize([(1, 'Alice'), (2, 'Bob'), (3, 'Charlie')])

# Save RDD as SequenceFile, a flat file containing binary key/value pairs.  
rdd.saveAsSequenceFile("output_sequence_file")

# Save RDD as Pickle files  
rdd.saveAsPickleFile("pickle_data")
```
# DataFrame
## Reading Files
[^2]
Even though reading from a file is technically a [[Spark - Transformations|transformation]], it is not evaluated lazily, and a job is triggered right away. (Remember __one action = one job__!)
	This is because, the read operation `spark.read` involves more complex logic such as inferring the schema, checking the file, and setting up data partitions.

## Writing Files

[^1]: https://medium.com/@sravangogineni19/dealing-with-different-file-formats-in-apache-spark-fad4865e9087

[^2]: https://medium.com/@ganeshmoorthy.va/understanding-sparks-handling-of-file-reads-rdd-vs-dataframe-25e9f436e72e

[^3]: https://proedu.co/spark/how-to-read-a-file-using-textfile-and-wholetextfiles-methods-in-apache-spark/

[^4]: https://glittercoin.wordpress.com/2018/08/15/what-is-the-difference-between-sc-textfile-and-sc-wholetextfiles-in-spark/

[^5]: https://medium.com/@singhsameer121295/exploring-pyspark-rdds-saving-data-in-different-file-formats-6fba8c07d45c
