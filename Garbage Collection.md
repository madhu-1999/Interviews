#java 
```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
# Prerequisite
+ [[JVM vs JRE vs JDK#**Stack area**|Stack Memory]]
+ [[JVM vs JRE vs JDK#**Heap Area**| Heap Memory]]
# Overview
+ Garbage collection is a mechanism of JVM to reclaim heap memory from objects that are eligible.
+ It is carried out by a [[Multithreading#Types of threads|daemon]] thread.
+ It cannot be forced. `System.gc()` and `Runtime.gc()` can request garbage collection but it may/may not happen.
+ Before removing an object, garbage collector thread invokes`finalize()` method of the object to perform any cleanup required.
# Eligibility for Garbage Collection
An object. is eligible for garbage collection if:
+ All references to that object are set to null
```java
Shape shape = new Shape();
shape = null;
// Now shape object in heap is eligible for gc
```
+ If parent reference is set to null and it is the only reference.
```java
//Animal is parent class of Dog
Animal animal = new Dog()
animal = null;
//dog object is eligible for gc
```
+ The object is created inside a block, the reference goes out of scope when block is exited.
```java
if(x == 2){
	Shape shape = new Shape();
	shape.doSomething();
}
x++;
// On exiting "if block" shape object reference goes out of scope so it is eligible for gc
```
+ If the object has no strong references but has weak/soft references.
# Types of References
## Strong reference
+ Default reference type
+ Object in heap memory is garbage collected only when the reference points to `null` .
+ `MyClass obj = new MyClass()`
## Weak reference
+ If an object in heap memory only has weak references it is marked for garbage collection by the garbage collector.
```java
MyClass obj = new MyClass()
WeakReference<MyClass> weakRef = new WeakReference<MyClass>(obj);
```
## Soft reference
+ If an object in heap memory has a soft reference and no strong references, it is garbage collected only when JVM runs out of memory.
```java
MyClass obj = new MyClass()
SoftReference<MyClass> softRef = new SoftReference<MyClass>(obj);
```

# Heap Generations for GC
![Heap Space in Java](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgOUvaHSJHv9NFigMWKUsHIJGv_3E87OPDshPRV1EZjDaElObnOp3NEZ7L6JlS03wpiRPaxUPOt87SJnsF8yz0yAKg0sFC26zJhpvW6FgRa0o3NHn7hNvTDhGJ_nj_dfXtutV8C6hcapMkT/s1600/Java+Heap.PNG)
Heap memory is divided into 3 parts for gc:
+ Young Generation: $S_0$ and $S_1$ together are called Survivor space
	+ Eden
	+ $S_O$
	+ $S_1$
+ Old generation
+ Permanent Generation: Holds metadata of classes, methods and string pool.
When an object is created in heap, it is created in Eden. Each object in heap has an age associated to it. When an object survives a gc cycle, age is incremented and it alternates between $S_0$ and $S_1$ space after surviving a gc cycle. When age crosses a threshold, the object is promoted to old generation.
GC in Young generation : minor gc (more frequent)
GC in old generation: major gc (less frequent)
# Types of GC
## Serial
+ Single thread to handle all gc tasks
+ App paussed when gc happens
+ Used in embedded/ small systems where memory is limited.
## Parallel
+ Default gc in Java 8
+ Multiple threads to handle gc tasks
+ Pauses app even for minor gc
+ Suitable for apps where high performance needed and can tolerate some pauses ex: batch processing
## Concurrent Mark and Sweep Collector
+ Multiple threads to handle gc tasks
+ Lets app run most of the time while gc happens, reducing pause time
+ Suitable for low latency apps ex: web services
## G1 Garbage Collector
+ default gc in Java 9
+ More efficient CMS with compaction
+ Designed for apps with large heaps
# Finalize
+ finalize() method is called before garbage collection happens, for cleanup purposes. (Think finally in try-catch).