#multithreading #java 
 
 A lock is an abstraction that allows at most one thread to _own_ it at a time. 
 _Holding a lock_ is how one thread tells other threads: “I’m changing this thing, don’t touch it right now.”

Locks have two operations:
- **`acquire`** allows a thread to take ownership of a lock. If a thread tries to acquire a lock currently owned by another thread, it _blocks_ until the other thread releases the lock. At that point, it will contend with any other threads that are trying to acquire the lock. At most one thread can own the lock at a time.  
- **`release`** relinquishes ownership of the lock, allowing another thread to take ownership of it.
# Intrinsic Locking
In Java, every object has a lock implicitly associated with it — a `String`, an array, an `ArrayList`, and every class you create, all of their object instances have a lock.
```java
Object lock = new Object();
```

You can’t call `acquire` and `release` on Java’s intrinsic locks, however. Instead you use the [synchronized Keyword](synchronized%20Keyword.md) to acquire the lock for the duration of a statement block:
```java
synchronized (lock) { // thread blocks here until lock is free
    // now this thread has the lock
    balance = balance + 1;
    // exiting the block releases the lock
}
```
## Monitor Pattern
>A monitor is a class whose methods are mutually exclusive, so that only one thread can be inside an instance of the class at a time.

When you are writing methods of a class, the most convenient lock is the object instance itself, i.e. `this`.
```java
public class SimpleBuffer {
    private String text;
    ...
    public SimpleBuffer() {
        text = "";
    }
    public synchronized void insert(int pos, String ins) {
        text = text.substring(0, pos) + ins + text.substring(pos);
    }
    public synchronized void delete(int pos, int len) {
        text = text.substring(0, pos) + text.substring(pos+len);
    }
    public synchronized int length() {
        return text.length();
    }
    public synchronized String toString() {
        return text;
    }
}
```
Read [synchronized Keyword](synchronized%20Keyword.md) to understand how this only allows one thread access to any method in the class at a time.
# Explicit Locking
These are advanced locks which you can control yourself (explicit `lock()` and `unlock()`).
```java
final Lock lock = new ReentrantLock();
try{
    lock.lock();
      //critical section
} finally {
    lock.unlock();
}
```
First a `Lock` is created. Then it's `lock()` method is called. Now the `Lock` instance is locked. Any other thread calling `lock()` will be blocked until the thread that locked the lock calls `unlock()`. Finally `unlock()` is called, and the `Lock` is now unlocked so other threads can lock it.

Imagine what happens if an exception is thrown between the call to `lock.lock()` and `lock.unlock()` . The exception would interrupt the program flow, and the call to `lock.unlock()` would never be executed. The Lock would thus remain locked forever.
To avoid exceptions locking a Lock forever, you should lock and unlock it from within a try-finally block.
```run-java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;
public class ThreadDriver {
    public static void main(String[] args) {
        CounterLock counterLock = new CounterLock();
        Runnable task = new Runnable() {
            @Override
            public void run() {
                counterLock.inc();
                System.out.println(Thread.currentThread().getName() + ": " + counterLock.getCount());
            }
        };
        Thread t1 = new Thread(task);
        Thread t2 = new Thread(task);

        t1.start();
        t2.start();
    }
}

class CounterLock {
    private long count = 0;
    private Lock lock = new ReentrantLock();

    public void inc() {
        try {
            lock.lock();
            this.count++;
        } finally {
            lock.unlock();
        }
    }

    public long getCount() {
        try {
            lock.lock();
            return this.count;
        } finally {
            lock.unlock();
        }
    }
}
```
# Reentrance
A lock is called _reentrant_ if the thread that holds the lock can lock it again. A non-reentrant lock is a lock which cannot be locked again if locked, not even by the thread that holds the lock.

The `ReentrantLock` class is a reentrant lock.
```run-java
import java.util.concurrent.locks.ReentrantLock;
import java.util.concurrent.locks.Lock;

public class ThreadDriver {
    public static void main(String[] args) {
        Calculator calculator = new Calculator();
        Runnable task = new Runnable() {
            @Override
            public void run() {
                Calculator.Calculation c1 = new Calculator.Calculation(0, 2);
                Calculator.Calculation c2 = new Calculator.Calculation(1, 1.5);
                calculator.calculate(c1, c2);
            }
        };

        Thread t1 = new Thread(task);
        t1.start();
    }
}

class Calculator {

    public static class Calculation {
        public static final int UNSPECIFIED = -1;
        public static final int ADDITION = 0;
        public static final int SUBTRACTION = 1;
        int type = UNSPECIFIED;

        public double value;

        public Calculation(int type, double value) {
            this.type = type;
            this.value = value;
        }
    }

    private double result = 0.0D;
    Lock lock = new ReentrantLock();

    public void add(double value) {
        try {
            lock.lock();
            this.result += value;
        } finally {
            lock.unlock();
        }
    }

    public void subtract(double value) {
        try {
            lock.lock();
            this.result -= value;
        } finally {
            lock.unlock();
        }
    }

    public void calculate(Calculation... calculations) {
        try {
            lock.lock();

            for (Calculation calculation : calculations) {
                switch (calculation.type) {
                    case Calculation.ADDITION:
                        add(calculation.value);
                        break;
                    case Calculation.SUBTRACTION:
                        subtract(calculation.value);
                        break;
                }
                System.out.println(result);
            }
        } finally {
            lock.unlock();
        }
    }
}
```
# References
https://web.mit.edu/6.005/www/fa15/classes/23-locks/#deadlock
https://jenkov.com/tutorials/java-concurrency
https://www.youtube.com/watch?v=ay5ynKnI9vc&list=PLA3GkZPtsafYhmrZR_1nmQqfFSnkftOud&index=8