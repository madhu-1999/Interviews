#java #multithreading 

# Basics
## Core
 ![[mx-img-eqk2ncb0iyx8hznwrbpv9ykr-pt1m29_99s.jpg|Multithreading Basics: CPU, Core, Program, Process, Thread & much more - 01:29|50]] [01:29](https://www.youtube.com/watch?v=091vJWjl1A4&t=90#t=01:29.99) 
 ![[mx-img-eqk2ncb0iyx8hznwrbpv9ykr-pt1m55_20s.jpg|Multithreading Basics: CPU, Core, Program, Process, Thread & much more - 01:55|50]] [01:55](https://www.youtube.com/watch?v=091vJWjl1A4&t=115#t=01:55.20) 
## Program
It is a set of instructions written in a programming language that tells the computer how to perform a specific task.
Ex: Microsoft Word allows users to create and edit documents.
## Process
It is an **instance** of a program that is being executed. 
## Thread
It is the **smallest** unit of execution within a process.
A process can have multiple threads which share the same resources but can run independently.
 ![[mx-img-eqk2ncb0iyx8hznwrbpv9ykr-pt4m24_31s.jpg|Multithreading Basics: CPU, Core, Program, Process, Thread & much more - 04:24|50]] [04:24](https://www.youtube.com/watch?v=091vJWjl1A4&t=264#t=04:24.31) 
## Multitasking
**Run multiple processes simultaneously** .
	In **single core CPUs**, done through time sharing (rapidly switching between processes).
	In **multi core CPUs**, processes distributed across cores with a scheduler balancing the load.
 ![[mx-img-eqk2ncb0iyx8hznwrbpv9ykr-pt7m56_96s.jpg|Multithreading Basics: CPU, Core, Program, Process, Thread & much more - 07:56|50]] [07:56](https://www.youtube.com/watch?v=091vJWjl1A4&t=477#t=07:56.96) 
## Multithreading
Refers to the ability to execute **multiple threads** in a single process concurrently.
	Every thread has its own call stack and copy of local variables but they share heap
 ![[mx-img-eqk2ncb0iyx8hznwrbpv9ykr-pt9m30_30s.jpg|Multithreading Basics: CPU, Core, Program, Process, Thread & much more - 09:30|50]] [09:30](https://www.youtube.com/watch?v=091vJWjl1A4&t=570#t=09:30.30) 
It makes **multitasking more efficient**, by **breaking a process down into threads** that are managed concurrently.

>[!info] How does JVM handle multithreading?
>JVM creates a **main thread**, which is responsible for executing the **main method**.
# Ways to create Thread
## By extending Thread class
+ `java.lang.Thread` contains a `run()` method which should be overriden by child class.
+ Calling `t1.run()` will ***NOT*** invoke the thread's `run()` method. 
+ On calling `t1.start()`, internally `run()` method is called.
+ Less Preferred as child class cannot extend another class.
```run-java
public class ThreadDriver {
	public static void main(String[] args) {

		Thread t1 = new MyThread();
		Thread t2 = new MyThread();
		
		t1.start();
		t2.start();
	}
}
class MyThread extends Thread{
	@Override
	public void run() {
		System.out.println(Thread.currentThread().threadId() + " Inside MyThread class");
	}
}
```
	## By implementing Runnable interface (Preferred)
+ Child class must override run() method in `java.lang.Runnable` interface.
+ We pass instance of Runnable (here, `new MyThread()`) to the Thread class constructor.
+ Calling `t1.run()` will ***NOT*** invoke the thread's `run()` method. 
+ On calling `t1.start()`, internally `run()` method is called.
+ Better than extending Thread class since child class can extend another class.
```run-java
public class ThreadDriver {
	public static void main(String[] args) {

		Thread t1 = new Thread(new MyThread());
		Thread t2 = new Thread(new MyThread());
		
		t1.start();
		t2.start();
	}
}

class MyThread implements Runnable{
	@Override
	public void run() {
		System.out.println(Thread.currentThread().threadId() + " Inside MyThread class");
	}
}
```
## Using anonymous class
```run-java
public class ThreadDriver {
    public static void main(String[] args) {
        Thread t1 = new Thread(new Runnable() {
            public void run() {
                System.out.println(Thread.currentThread().threadId() + " Inside Thread class 1");
            }
        });

        Thread t2 = new Thread(new Runnable() {
            public void run() {
                System.out.println(Thread.currentThread().threadId() + " Inside Thread class 2");
            }
        });

        t1.start();
        t2.start();
    }
}
```
## Using lambda
```run-java
public class ThreadDriver {
	public static void main(String[] args) {
		Thread t1 = new Thread(() -> {
			System.out.println(Thread.currentThread().threadId() + " Inside Thread class 1");
		});

		Thread t2 = new Thread(() -> {
			System.out.println(Thread.currentThread().threadId() + " Inside Thread class 2");
		});
		
		t1.start();
		t2.start();
	}
}
```
# Types of threads

|Feature|**User Thread**|**Daemon Thread**|
|---|---|---|
|**Purpose**|Performs main program logic (e.g., request handling, computation)|Supports background tasks (e.g., garbage collection, monitoring)|
|**Prevents JVM Exit?**|**Yes** – JVM waits for user threads to finish|**No** – JVM terminates daemon threads when no user threads are running|
|**Created by Default?**|Yes (e.g., `main` thread, explicitly created threads)|No (must be explicitly set using `.setDaemon(true)`)|
|**Lifespan**|Runs until it completes|Terminates when JVM exits (even if still running)|
|**Example Use Cases**|Handling user requests, processing data, main application logic|Garbage collection (`GC`), logging, periodic cleanup tasks, thread pool workers|
![main thread in java](https://media.geeksforgeeks.org/wp-content/uploads/main-thread-in-java.jpeg)
# Important Thread class functions

| Method                        | Action                                                                        |
| ----------------------------- | ----------------------------------------------------------------------------- |
| getId() / setId()             | Gets/Sets thread id                                                           |
| getName() /setName()          | Gets/Sets thread name                                                         |
| getPriority() / setPriority() | Gets/Sets thread priority. MIN_PRIORITY = 1, NORM_PRIORITY=5, MAX_PRIORITY=10 |
| currentThread()               | Returns reference to current thread (static method)                           |
| getState() / setState()       | Gets/Sets thread state                                                        |
| interrupt()                   | Interrupts thread                                                             |
| isAlive()                     | Checks if thread is alive                                                     |
| isDaemon()                    | Checks if thread is daemon thread                                             |
| join()                        | Parent thread waits for thread to finish execution                            |
| yield()                       | Thread yields processor to next thread waiting                                |
| sleep()                       | Puts thread to sleep for specified ms                                         |

# Thread Lifecycle and States
![Lifecycle-and-States-of-a-Thread-in-Java](https://media.geeksforgeeks.org/wp-content/uploads/20240318155846/Lifecycle-and-States-of-a-Thread-in-Java-1.png)
## New
`public static final Thread.State NEW`
+ When thread is created it is in this state and has ***NOT*** started to run.
## Runnable
`public static final Thread.State RUNNABLE`
+ When thread is ready to run or running, it is in this state
## Blocked
`public static final Thread.State BLOCKED`
+ When thread is trying to acquire lock, it is in this state. 
+ Moves to Runnable when lock is acquired.
## Waiting
`public static final Thread.State WAITING`
+ When wait() or join() is called, it is in this state
+ Moves to Runnable when thread finishes execution or notify() is called on waiting thread.
## Timed Waiting
`public static final Thread.State TIMED_WAITING`
+ When sleep() or other timed parameter method is called, thread is in this state
+ Moves to Runnable when sleep finishes or conditional wait is over.
## Terminated
`public static final Thread.State TERMINATED`
+ Thread terminates either due to finishing execution or due to some exception / segmentation fault.

>[!warning] 
>A thread **cannot be restarted** once it has completed its execution. Once a thread reaches the `TERMINATED` state, it is considered "dead" and an attempt to call the `start()` method again will result in an `IllegalThreadStateException`.

```run-java
public class ThreadDriver {
    public static void main(String[] args) throws InterruptedException {
        MyThread t1 = new MyThread();
        System.out.println(t1.getState()); // NEW

        t1.start();
        System.out.println(t1.getState()); // RUNNABLE

        // Main thread sleeps. Done so that thread t1 gets a chance to enter next state,
        // before main thread prints thread state
        Thread.sleep(1000);
        System.out.println(t1.getState()); // TIMED_WAITING

        t1.join();
        System.out.println(t1.getState()); // TERMINATED

    }
}

class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("RUNNING");
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
```
# Thread Priority
| Constant                        | Description                                              |
| ------------------------------- | -------------------------------------------------------- |
| public static int NORM_PRIORITY | Sets the default priority for the Thread. (Priority: 5)  |
| public static int MIN_PRIORITY  | Sets the Minimum Priority for the Thread. (Priority: 1)  |
| public static int MAX_PRIORITY  | Sets the Maximum Priority for the Thread. (Priority: 10) |
+ Main thread has default priority of 5. 
+ Default priority of all other threads depends on parent thread priority.
+ Threads with higher priority execute first.
+ Threads with same priority, scheduler algorithm chooses which executes first.
# Common race conditions

>[!warning]
>The Java Memory Model (JMM) does not guarantee when changes made by one thread become visible to another
## Read-modify-write critical sections:
Imagine if two threads, A and B, are executing the add method on the same instance of the `Counter` class. There is no way to know when the operating system switches between the two threads. 

The code in the `add()` method is not executed as a single atomic instruction by the Java virtual machine. Observe what happens with the following mixed execution of threads A and B:
   A:  Reads this.count into a register (0)
   B:  Reads this.count into a register (0)
   B:  Adds value 2 to register
   B:  Writes register value (2) back to memory. this.count now equals 2
   A:  Adds value 3 to register
   A:  Writes register value (3) back to memory. this.count now equals 3
   
```run-java ln:True
public class ThreadDriver {
    public static void main(String[] args) {
        Counter counter = new Counter();
        Thread t1 = new Thread(new Runnable() {
            public void run() {
                counter.add(2);
            }
        });

        Thread t2 = new Thread(new Runnable() {
            public void run() {
                counter.add(3);
            }
        });

        t1.start();
        t2.start();
        // Main thread might execute this command, before both threads finish.
        System.out.println(counter.count);
    }
}

class Counter {

    protected long count = 0;

    public void add(long value) {
        this.count = this.count + value;
    }
}
```
## Check then act critical section
If two or more threads call the `checkThenAct()` method on the __same__ object, then two or more threads may execute the if-statement at the same time, evaluate `sharedMap.containsKey("key")` to `true`, and thus move into the body code block of the if-statement. In there, multiple threads may then try to remove the key, value pair stored for the key "key", but only one of them will actually be able to do it. The rest will get a `null` value back, since another thread already removed the key, value pair.
```java hl:5-14
import java.util.HashMap;
import java.util.Map;

public class ThreadDriver {
    public void checkThenAct(Map<String, String> sharedMap) {
        if (sharedMap.containsKey("key")) {
            String val = sharedMap.remove("key");
            if (val == null) {
                System.out.println("Value for key was null");
            }
        } else {
            sharedMap.put("key", "value");
        }
    }

    public static void main(String[] args) {
        ThreadDriver driver = new ThreadDriver();
        Map<String, String> sharedMap = new HashMap<>();

        Thread t1 = new Thread(new MyThread(driver, sharedMap));
        Thread t2 = new Thread(new MyThread(driver, sharedMap));

        t1.start();
        t2.start();
    }
}

class MyThread implements Runnable {
    ThreadDriver driver;
    Map<String, String> sharedMap;

    public MyThread(ThreadDriver driver, Map<String, String> sharedMap) {
        this.driver = driver;
        this.sharedMap = sharedMap;
    }

    public void run() {
        driver.checkThenAct(sharedMap);
    }
}
```
## Lazy Initialization
If two threads call the `getInstance()` method concurrently: 
1. **Thread 1** calls `getInstance()`. It checks `if (singleton == null)` and finds it is `true`.
2. The operating system switches execution to **Thread 2** _before_ Thread 1 can create the instance.
3. **Thread 2** calls `getInstance()`. It checks `if (singleton == null)` and also finds it is `true` (because Thread 1 hasn't assigned it yet).
4. **Thread 2** proceeds to create a new `LazySingleton` instance and assigns it to the `singleton` variable.
5. The operating system switches back to **Thread 1**.
6. **Thread 1** proceeds with its interrupted execution, creates a _second_ new `LazySingleton` instance, and overwrites the `singleton` variable that Thread 2 just set.
```run-java
import java.util.HashMap;
import java.util.Map;

public class ThreadDriver {
    public static void main(String[] args) {

        Thread t1 = new Thread(new MyThread());
        Thread t2 = new Thread(new MyThread());

        t1.start();
        t2.start();
    }
}

class LazySingleton {
    private static LazySingleton singleton = null;

    public static LazySingleton getSingleton() {
        if (singleton == null) {
            return new LazySingleton();
        }
        return singleton;
    }
}

class MyThread implements Runnable {

    public MyThread() {
    }

    public void run() {
        LazySingleton singleton = LazySingleton.getSingleton();
        System.out.println(singleton);
    }
}
```
## Data visibility race
Definition:
	A data visibility race condition occurs when one thread writes data to a shared variable but fails to properly synchronize that change, meaning a second thread might read an outdated (stale) value of that variable from its local CPU cache instead of the main memory.

1. **Thread 1 (Writer)** reads the `keepRunning` flag into its own CPU cache.
2. The `while(keepRunning)` loop executes very quickly using the cached value (`true`).
3. **Thread 2 (Main)** updates the _main memory_ version of `keepRunning` to `false`.
4. **Thread 1** is never told that the main memory value changed. It continues to check its local CPU cache value, which is still `true`.

The program hangs indefinitely because the data change is not made _visible_ to the second thread.
```java hl:5-10
public class ThreadDriver {
    private static boolean keepRunning = true;
    public static void main(String[] args) {

        Thread t1 = new Thread(() -> {
            long count = 0;
            while (keepRunning) {
                count++;
            }
        });

        t1.start();

        try {
            Thread.sleep(100);
            System.out.println("Main thread is setting keepRunning to false.");
            keepRunning = false;

            t1.join(); // Wait for the writer thread to finish
            System.out.println("Main thread finished.");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```
## Order Violation Race
Defintion:
	An **order violation race condition** occurs when the correct functioning of a program relies on two or more operations happening in a specific sequence, but concurrent execution allows for an incorrect ordering, leading to a bug.

Suppose, that we have `count` variable with value __10__. Now we first want to increment it by 100 and then reset its value to 0. But as seen, that order is violated. __Thread t2__ executes first, followed by __Thread t1__. This causes final output to be 100 instead of 0.
```run-java
public class ThreadDriver {
    private static Integer count = 10;

    public static void increment() {
        try {
            Thread.sleep(10);
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        for (int i = 0; i < 100; i++) {
            count++;
        }
    }

    public static void reset() {
        System.out.println("In t2, Count is: " + count);
        count = 0;
    }

    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
            ThreadDriver.increment();
        });

        Thread t2 = new Thread(() -> {
            ThreadDriver.reset();
        });

        t1.start();
        t2.start();

        t1.join(); // Wait for t1 to finish
        t2.join(); // Wait for t2 to finish
        System.out.println("After t1, Count is: " + ThreadDriver.count);

    }
}
```

# Thread Safety and Shared Resources
Code that is safe to call by multiple threads simultaneously is called _thread safe_. 
If a piece of code is thread safe, then it contains no [[#Common race conditions|race conditions]] .
## Local Variables
Every thread stores its own copy of local variables on its stack.
	i.e. local variables are **not shared** between threads.
```run-java
public class ThreadDriver {
    public static void main(String[] args) throws InterruptedException {
        MyThread t1 = new MyThread();
        MyThread t2 = new MyThread();

        t1.start();
        t2.start();

        t1.join();
        t2.join();

    }
}

class MyThread extends Thread {
    @Override
    public void run() {
		someMethod();
    }

    public void someMethod() {
        long threadSafeInt = 0;
        threadSafeInt++;
        System.out.println(threadSafeInt);
    }
}
```

## Local Object References
**The object reference is not shared** since it is local in nature.
**The object *being* referenced** is however, on the **shared heap**.
	All objects in Java are created in the shared heap.

The ***reference*** is thread safe, as long as:
1. It never escapes the method
2. It is passed to other methods or objects, such that those methods/objects are not available to other threads.

Example of thread safe local object reference:
```run-java
public class ThreadDriver {
    public static void main(String[] args) throws InterruptedException {
        MyThread t1 = new MyThread();
        MyThread t2 = new MyThread();
        t1.start();
        t2.start();

        t1.join();
        t2.join();

    }
}

class MyThread extends Thread {
    @Override
    public void run() {
        someMethod();
    }

    public void someMethod() {

        Object localObject = new Object();

        // Calling methods using this reference is safe
        System.out.println(Thread.currentThread().getName() + ": " + localObject.getClass());
        method2(localObject);
    }

    public void method2(Object localObject) {
        // Two different objects will return different hashcode
        System.out.println(Thread.currentThread().getName() + ": " + localObject.hashCode());
    }

}
```

## Object Member Variables
Object member variables (fields) are **stored on the heap** along with the object.
Therefore, if **two threads call a method on the same object instance** and this method _updates_ object member variables, **the method is not thread safe**.

Here is an example of non thread safe object member variable update:
```run-java
public class ThreadDriver {
    public static void main(String[] args) throws InterruptedException {
        NotThreadSafe sharedInstance = new NotThreadSafe();
        // Both threads share the same object instance -> proper thread safety required
        MyThread t1 = new MyThread(sharedInstance);
        MyThread t2 = new MyThread(sharedInstance);
        t1.start();
        t2.start();

        t1.join();
        t2.join();
        System.out.println(sharedInstance.builder.toString());

    }
}

class MyThread extends Thread {
    NotThreadSafe sharedInstance;

    public MyThread(NotThreadSafe sharedInstance) {
        this.sharedInstance = sharedInstance;
    }

    @Override
    public void run() {
        sharedInstance.add(Thread.currentThread().getName());
    }

}

class NotThreadSafe {
    StringBuilder builder = new StringBuilder();

    public void add(String text) {
        this.builder.append(text);
    }
}
```
## Immutable Objects
We can make sure that objects shared between threads are never updated by any of the threads by making the shared objects immutable, and thereby thread safe.
```java
public class ImmutableValue{
  private int value = 0;

  public ImmutableValue(int value){
    this.value = value;
  }

  public int getValue(){
    return this.value;
  }
  
  public ImmutableValue add(int valueToAdd){
      return new ImmutableValue(this.value + valueToAdd);
	}
}
```

Notice how the value for the `ImmutableValue` instance is passed in the constructor. Notice also how there is no setter method. Once an `ImmutableValue` instance is created you cannot change its value. It is immutable. You can read it however, using the `getValue()` method.
If you need to perform operations on the `ImmutableValue` instance you can do so by returning a new instance with the value resulting from the operation. Notice how the `add()` method returns a new `ImmutableValue` instance with the result of the add operation, rather than adding the value to itself.

### **The Reference is not Thread Safe!**
```java
public class Calculator{
  private ImmutableValue currentValue = null;

  public ImmutableValue getValue(){
    return currentValue;
  }

  public void setValue(ImmutableValue newValue){
    this.currentValue = newValue;
  }

  public void add(int newValue){
    this.currentValue = this.currentValue.add(newValue);
  }
}
```

The `Calculator` class holds a reference to an `ImmutableValue` instance. Notice how it is possible to change that reference through both the `setValue()` and `add()` methods. Therefore, even if the `Calculator` class uses an immutable object internally, it is not itself immutable, and therefore not thread safe. In other words: The `ImmutableValue` class is thread safe, but the **use of it** is not.
# 
>[!note] Preventing Race conditions
>If multiple threads access the same mutable state variable without appropriate synchronization, your program is broken. There are three ways to fix it: 
>+  Don't share the state variable across threads; 
>+  Make the state variable immutable; or 
>+ Use synchronization whenever accessing the state variable.

# Happens before Guarantee
## Instruction Reordering
Modern CPUs have the ability to execute instructions in parallel if the instructions do not depend on each other. For instance, the following two instructions do not depend on each other, and can therefore be executed in parallel:
>a = b + c
> d = e + f

However, the following two instructions cannot easily be executed in parallel, because the second instruction depends on the result of the first instruction:
>a = b + c
>d = a + e

Imagine the two instructions above were part of a larger set of instructions, like the following:
>a = b + c
>d = a + e
>l = m + n
>y = x + z

The instructions could be reordered like below. Then the CPU can execute at least the first 3 instructions in **parallel**, and as soon as the first instructions is finished, it can start executing the 4th instruction.
>a = b + c
>l = m + n
>y = x + z
>
>d = a + e

As you can see, reordering instructions can increase parallel execution of instructions in the CPU. Increased parallelization means increased performance.
## Problems With Instruction Reordering
```java
public void storeFrame(Frame frame) {
        this.frame = frame;
        this.framesStoredCount++;
        this.hasNewFrame = true;
}
```
Imagine multiple threads access the `storeFrame()` method.
Notice how the three instructions inside the `storeFrame()` method seem like they do not depend on each other. 
That means, that to the Java VM and the CPU it looks like it would be okay to reorder the instructions, if it determines that it would advantageous. However, imagine what would happen if the instructions were reordered, like this:
```java
public void storeFrame(Frame frame) {
        this.hasNewFrame = true;
        this.framesStoredCount++;
        this.frame = frame;
    }
```
Notice how the field `hasNewFrame` is now set to `true` before the `frame` field is assigned to reference the new Frame object. That means, that if the drawing thread is waiting in the while-loop in the `takeFrame()` method, the drawing thread could exit that while-loop, and take the old Frame object. That would result in a redrawing of an old Frame, leading to a waste of resources.
# Synchronization
# [[synchronized Keyword]]
# [[Volatile Keyword]]
# [[Lock]]


# References
https://jenkov.com/tutorials/java-concurrency/index.html