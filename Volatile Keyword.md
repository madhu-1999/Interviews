#java #multithreading 
# Variable Visibility
>With non-volatile variables there are **no guarantees** about when the Java Virtual Machine (JVM) **reads data from main memor**y into CPU registers, or **writes data** from CPU registers **to main memory**.
>><mark><b>The updates of one thread are not visible to other threads</b></mark>


Imagine *Thread 1* (on CPU core 1) writes to `counter` variable and *Thread 2* (on CPU core 2) reads `counter` variable.[^1]

Lets consider a situation where *Thread 2* reads `counter=0` value into its cache. Then *Thread 1* also reads in the current `counter=0` value into its cache to update it to `counter=7`. JVM however, doesn't guarantee when latest value of `counter` is flushed back to main memory, so if *Thread 2* reads `counter` value again, it will fetch the value from its cache (CPU register) = 0, instead of the actual latest value = 7.
If another *Thread 3* (on CPU core 3) tries to read `counter` value it will read in value = 0 since updated value = 7 hasn't been written to main memory.

![[Pasted image 20260327140405.png]]

# `Volatile`  Visibility Guarantee

The Java `volatile` keyword is intended to address variable visibility problems. 
By declaring the `counter` variable `volatile` **all writes** to the `counter` variable will be **written back to main memory immediately**. Also, **all reads** of the `counter` variable will be **read directly from main memory**.
 ```java
 public class SharedObject {
    public volatile int counter = 0;
}
 ```

## Full volatile Visibility Guarantee
>If Thread A writes to a `volatile` variable and Thread B subsequently reads the same volatile variable, then all variables visible to Thread A before writing the volatile variable, will also be visible to Thread B after it has read the volatile variable.

```java
this.nonVolatileVarA = 34;
this.nonVolatileVarB = new String("Text");
this.volatileVarC    = 300;
```
When the third instruction in the example above writes to the volatile variable `volatileVarC`, the values of the two non-volatile variables will also be synchronized to main memory - because these variables are visible to the thread when writing to the volatile variable.

If `volatileVarC` is updated before `nonVolatileVarB`, then the old value of `nonVolatileVarB` is flushed to main memory and there is **no guarantee** when its updated value will be written to main memory.

>If Thread A reads a `volatile` variable, then all all variables visible to Thread A when reading the `volatile` variable will also be re-read from main memory.

```java
c = other.volatileVarC;
b = other.nonVolatileB;
a = other.nonVolatileA;
```
Notice that the first instruction is a read of a `volatile` variable (`other.volatileVarC`). When `other.volatileVarC` is read in from main memory, the `other.nonVolatileB` and `other.nonVolatileA` are also read in from main memory.

If `other.volatileVarC` is read after `other.nonVolatileB`, then the value of `other.nonVolatileB` is read from cache (which may be stale) and the values of `other.volatileVarC` and `other.nonVolatileVarA` are read from main memory.
# Volatile Happens-Before Guarantee
Refer [[Multithreading#Instruction Reordering|Instruction Reordering]] before continuing
>To address the instruction reordering challenge, the Java `volatile` keyword gives a "happens-before" guarantee, in addition to the visibility guarantee. The happens-before guarantee guarantees that:
>     > Reads from and writes to other variables cannot be reordered to occur after a write to a `volatile` variable, if the reads / writes originally occurred before the write to the `volatile` variable.  
>
>     >The reads / writes before a write to a `volatile` variable are guaranteed to "happen before" the write to the `volatile` variable. Notice that it is still possible for e.g. reads / writes of other variables located after a write to a `volatile` to be reordered to occur before that write to the `volatile`. Just not the other way around. **From after to before is allowed, but from before to after is not allowed**.

# Limitation
>[!tldr] 
>1. If one thread writes and multiple threads read a `volatile` variable, then it is **safe**.
>2. If multiple threads write to a `volatile` variable, it is **unsafe**.

As soon as a thread needs to first read the value of a `volatile` variable, and based on that value generate a new value for the shared `volatile` variable, a `volatile` variable is no longer enough to guarantee correct visibility. The short time gap in between the reading of the `volatile` variable and the writing of its new value, creates an [[Multithreading#Common race conditions|race condition]] where multiple threads might read the same value of the `volatile` variable, generate a new value for the variable, and when writing the value back to main memory - overwrite each other's values.

Imagine if Thread 1 reads a shared `counter` variable with the value 0 into its CPU register, increment it to 1 and not write the changed value back into main memory. Thread 2 could then read the same `counter` variable from main memory where the value of the variable is still 0, into its own CPU register. Thread 2 could then also increment the counter to 1, and also not write it back to main memory.
![[Pasted image 20260327170406.png]]
## Solutions
1. One way is to use [[synchronized Keyword#`synchronized` keyword|synchronized]] to guarantee that the reading and writing of the variable is atomic, since `volatile` does not block threads reading or writing.
2. You could also use one of the many atomic data types found in the [`java.util.concurrent` package](https://jenkov.com/java-util-concurrent/index.html). For instance, the [`AtomicLong`](https://jenkov.com/java-util-concurrent/atomiclong.html) or [`AtomicReference`](https://jenkov.com/java-util-concurrent/atomicreference.html) or one of the others.
# Performance Considerations
1. Reading from and writing to main memory is more expensive than accessing the CPU register.
2. Accessing volatile variables also prevent instruction reordering which is a normal performance enhancement technique.
Thus, you should only use volatile variables when you really need to enforce visibility of variables.
[^1]: [[Multithreading#Basics#Core]]
