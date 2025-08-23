#java 
+ Streams are data pipelines through which elements in arrays, collection objects, generator functions pass through. 
+ Various operations like sorting, filtering, mapping can be performed on the elements in the pipeline, they are called **intermediate** operations.
+ **Intermediate** operations are **evaluated lazily**, i.e. only when terminal functions are invoked.
+ **Terminal** operations like collect(), reduce(), count() trigger processing of stream, and **generate the output** to be returned. They **close the stream**.
+ Generally used for bulk, parallel processing.
+ Streams **do not** store data.

# Ways to create a stream
## From collection
```java
Stream<Integer> stream = salaryList.stream();
```
## From arrays
```java
Stream<Integer> stream = Arrays.stream(salaryArray);
```
## From static method
```java
Stream<Integer> stream = Stream.of(1000, 5000, 300, 203);
```
## From Stream Builder
```java
Stream.Builder<Integer> stream = Stream.builder();
stream.add(1000).add(3000).add(300).add(203);
```
## From Stream Iterate
```java
Stream<Integer> stream = Stream.iterate(1000, (Integer n) -> n+500).limit(5); // 1500,2000,2500,3000,3500
```