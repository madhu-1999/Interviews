#java #multithreading
# `synchronized` keyword

Synchronized blocks in Java are marked with the `synchronized` keyword.
A synchronized block in Java is synchronized on **some object**.
	All  synchronized blocks synchronized on the **same object** can only have **one thread executing inside them at the same time.**
	All other threads attempting to enter the synchronized block are blocked until the thread inside the thread exits the `synchronized` block

# Ways to synchronize
## Synchronized Instance Methods
Here is a synchronized instance method:
```java hl:5
public class MyCounter {

  private int count = 0;

  public synchronized void add(int value){
      this.count += value;
  }
}

```
A synchronized instance method in Java is synchronized on the instance (object) owning the method.
	Only **one thread** can execute at a time per instance.
If there is **one instance** of `MyCounter` and two threads try to execute `add()` method: only one thread can enter the method at a time.

If there are **two instances** of `MyCounter`  _M1_ and _M2_, and if thread _A_ is trying to execute _M1's_ `add()` method, while thread _B_ is trying to execute _M2's_ `add()` method, then **both threads A and B** can enter the `add()` method, since they are using **different instances**.

> [!faq] But what if there is only one instance but more than one instance method?

```java
public class MyCounter {
  private int count = 0;

  public synchronized void add(int value){
      this.count += value;
  }
  public synchronized void subtract(int value){
      this.count -= value;
  }
}
```

Lets say thread _A_ is trying to enter `add()` method and thread _B_ is trying to enter `subtract()` method. 
	**Only one** thread can enter per instance i.e. if thread _A_ is executing `add()` method then thread _B_ **cannot** enter `subtract()` method.
## Synchronized Static Methods
```java
public static MyStaticCounter{
  private static int count = 0;
  
  public static synchronized void add(int value){
      count += value;
  }
  
  public static synchronized void subtract(int value){
    count -= value;
  }
}
```
Synchronized static methods are synchronized on the class object of the class the synchronized static method belongs to. 
	Since there exists **only one class object per class**, only **one thread** can execute for a class.
If two threads try to enter `add()` method: 
	only one can enter at a time.
If thread _A_ is trying to enter `add()` method and thread _B_ is trying to enter `subtract()` method: 
	Only one thread can execute inside any of the two `add()` and `subtract()` methods at any given time. If Thread A is executing `add()` then Thread B cannot execute neither `add()` nor `subtract()` until Thread A has exited `add()`.

## Synchronized Blocks in Instance Methods
Used when entire method need not be synchronized. Only the portion with shared resources should be.
```java
public void add(int value){
    synchronized(this){
       this.count += value;   
    }
  }
```
Notice how the Java synchronized block construct takes an object in parentheses. In the example `this` is used, which is the instance the add method is called on.
The object taken in the parentheses by the synchronized construct is called a **monitor object**.
	Only **one thread** can execute inside a Java code block **synchronized on the same monitor object**.
## Synchronized Blocks in Static Methods
```java
public static void log2(String msg1, String msg2){
       synchronized(MyClass.class){
          log.writeln(msg1);
          log.writeln(msg2);  
       }
    }
```
Notice how the Java synchronized block construct takes an object in parentheses. In the example `MyClass.class` is used, which is a class instance. (It may or may not be the same class in which `log2()` is defined.) 
The object taken in the parentheses by the synchronized construct is called a **monitor object**.
	Only **one thread** can execute inside a Java code block **synchronized on the same monitor object**.
# What Objects to Not Synchronize On
It is recommended that you do not synchronize on **String** objects, or any **primitive type wrapper objects**, as the compiler might optimize those.
# Data Visibility
>When a thread **enters** a synchronized block it will **refresh the values of all variables visible to the thread**. 
```java
public void get(Values v) {
        synchronized(this) {
            v.valC = this.valC;
        }
        v.valB = this.valB;
        v.valA = this.valA;
}
```
In the `get()` method the synchronized block is placed at the beginning of the method. When the thread calling `get()` enters the synchronized block, all variables are re-read in from main memory. That is why this synchronized block is placed at the beginning of the method - to guarantee that all variables are refreshed from main memory before they are read.

>When a thread **exits** a synchronized block **all changes to variables visible to the thread will be committed to main memory**.
```java
public void set(Values v) {
        this.valA = v.valA;
        this.valB = v.valB;

        synchronized(this) {
            this.valC = v.valC;
        }
}
```
In the `set()` method the synchronized block at the end of the method will force all the variables to be synchronized to main memory after being updated. This flushing of the variable values to main memory happens when the thread exits the synchronized block. That is the reason it has been placed last in the method - to guarantee that all updated variable values are flushed to main memory
# Instruction Reordering
The `synchronized` keyword places some restrictions on reordering of instructions before, inside and after synchronized blocks.
	 **No instruction reordering** takes place.
# Performance Overhead
There is a **small performance overhead** associated with entering and exiting a `synchronized` block.
	Only synchronize the operations that are really necessary to synchronize - to avoid blocking other threads from executing operations that do not have to be synchronized.
	This increases **parallelism** of code.
# Reentrance
Once a thread has entered a synchronized block the thread is said to "hold the lock" on the monitoring object the synchronized block is synchronized on. 

If the thread calls another method which calls back to the first method with the synchronized block inside, the thread holding the lock can reenter the synchronized block. It is not blocked just because a thread (itself) is holding the lock. 
```java
public class Reentrant{

 // Thread can reenter outer again after being called from inner since it holds the lock on monitor object (this).
  public synchronized outer(){
    inner();
  }

  public inner(){
    //do something
    outer();
  }
}
```

if a Java thread enters a synchronized block of code, and thereby takes the lock on the monitor object, the block is synchronized on, the thread can enter other Java code blocks synchronized on the same monitor object.
```java
public class Reentrant{

  public synchronized outer(){
    inner();
  }

//Thread can enter inner() since both outer() and inner() are synchronized on the same object. 
  public synchronized inner(){
    //do something
  }
}
```
	