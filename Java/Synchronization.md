#java 
+ Ensures that multiple threads access shared resources safely.
+ Prevents race conditions.
+ Without synchronization, shared resources would be inconsistent or corrupted.

# Ways to synchronize
## Synchronized block
+ Block {} can be synchronized using `synchronized` keyword. 
+ Only one thread can enter at a time. All other threads or **blocked** until current thread exits synchronized block.
+ Used when entire method need not be synchronized. Only the portion with shared resources should be.
```java
class Counter {
    private int counter;
    public Counter() {
        counter = 0;
    }
    public int getCounter() {
        return counter;
    }

    public void increment() {
        synchronized(this) {
            counter++;
        }
    }
}

public class  ThreadDriver {
    public static void main(String[] args) {
        Counter counter = new Counter();
        Thread t1 = new Thread(() -> {
            for(int i=0; i<1000; i++) {
                counter.increment();
            }
        });
        Thread t2 = new Thread(() -> {
            for(int i=0; i<1000; i++) {
                counter.increment();
            }
        });

        t1.start();
        t2.start();

        try {
            t1.join();
            t2.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    
        System.out.println("Counter: "+counter.getCounter());
    }
    
}
// op: Counter: 2000
```
## Synchronized method
+ Method synchronized using `synchronized` keyword.
+ Only one thread can enter at a time. All other threads or **blocked** until current thread exits synchronized block.
+ Used when method is only performing operations on shared variables and doing nothing else.
```java
class Counter {
    private int counter;
    public Counter() {
        counter = 0;
    }
    public int getCounter() {
        return counter;
    }

    public synchronized void increment() {
        counter++;
    }
}

public class  ThreadDriver {
    public static void main(String[] args) {
        Counter counter = new Counter();
        Thread t1 = new Thread(() -> {
            for(int i=0; i<1000; i++) {
                counter.increment();
            }
        });
        Thread t2 = new Thread(() -> {
            for(int i=0; i<1000; i++) {
                counter.increment();
            }
        });

        t1.start();
        t2.start();

        try {
            t1.join();
            t2.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    
        System.out.println("Counter: "+counter.getCounter());
    }
    
}
```

# Connected Topics:
1. [[Multithreading]]