#java 
+ It is a feature that allows concurrent execution of parts of the program. 
+ Concurrent execution is carried out by threads, which are lightweight processes that represent a sequence of execution.
+ Every thread has its own call stack and copy of local variables but they share heap
```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
# Ways to create Thread
## By extending Thread class
+ `java.lang.Thread` contains a `run()` method which should be overriden by child class.
+ Calling `t1.run()` will ***NOT*** invoke the thread's `run()` method. 
+ On calling `t1.start()`, internally `run()` method is called.
+ Less Preferred as child class cannot extend another class.
```java
class MyThread extends Thread{
	@Override
	public void run() {
		System.out.println(Thread.currentThread().getId() + " Inside MyThread class");
	}
}

public class ThreadDriver {
	public static void main(String[] args) {

		Thread t1 = new MyThread();
		Thread t2 = new MyThread();
		
		t1.start();
		t2.start();
	}
}
```
## By implementing Runnable interface (Preferred)
+ Child class must override run() method in `java.lang.Runnable` interface.
+ We pass instance of Runnable (here, `new MyThread()`) to the Thread class constructor.
+ Calling `t1.run()` will ***NOT*** invoke the thread's `run()` method. 
+ On calling `t1.start()`, internally `run()` method is called.
+ Better than extending Thread class since child class can extend another class.
  ```java
class MyThread extends Runnable{
	@Override
	public void run() {
		System.out.println(Thread.currentThread().getId() + " Inside MyThread class");
	}
}

public class ThreadDriver {
	public static void main(String[] args) {

		Thread t1 = new Thread(new MyThread());
		Thread t2 = new Thread(new MyThread());
		
		t1.start();
		t2.start();
	}
}
```
## Using anonymous class
```java
public class ThreadDriver {
	public static void main(String[] args) {
		Thread t1 = new Thread(() -> {
			System.out.println(Thread.currentThread().getId() + " Inside Thread class 1");
		});

		Thread t2 = new Thread(() -> {
			System.out.println(Thread.currentThread().getId() + " Inside Thread class 2");
		});
		
		t1.start();
		t2.start();
	}
}
/* O/P 
14 Inside Thread class 1
15 Inside Thread class 2
*/
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

# Thread Priority
|Constant|Description|
|---|---|
|public static int NORM_PRIORITY|Sets the default priority for the Thread. (Priority: 5)|
|public static int MIN_PRIORITY|Sets the Minimum Priority for the Thread. (Priority: 1)|
|public static int MAX_PRIORITY|Sets the Maximum Priority for the Thread. (Priority: 10)|
+ Main thread has default priority of 5. 
+ Default priority of all other threads depends on parent thread priority.
+ Threads with higher priority execute first.
+ Threads with same priority, scheduler algorithm chooses which executes first.
# Volatile Keyword
+ Provides **Lock-Free** synchronization of fields (i.e. instance or class variables).
+ Can ***ONLY*** be applied to fields (i.e instance or class variables).
+ ***DOES NOT*** apply to methods or local variables since all threads have their own copy of local variables.
+ JIT or JVM reorders/ optimizes code during compilation but the **volatile** keyword signals to the JVM not to reorder or change order of instructions.
+ It  provides a **visibility guarantee**. i.e value of volatile field is read by threads from main memory each time, they don't cache the value.
+ It ***DOES NOT*** provide **mutual exclusion** or **atomicity**.
+ Use volatile modifier if you just need to synchronize access to a shared variable whose value is set by one thread and queried by other.
+ Use to make reading **long/double** value read atomic 