
 <h1>Java Threads and the <code>Runnable</code> Interface</h1>

  <h2>Table of Contents</h2>
  <ul>
    <li class="li"><a href="#what-are-threads">What are Threads?</a></li>
    <li><a href="#creating-threads">Creating Threads</a>
      <ul>
        <li><a href="#using-the-runnable-interface">Using the <code>Runnable</code> Interface</a></li>
        <li><a href="#using-the-thread-class">Using the <code>Thread</code> Class</a></li>
      </ul>
    </li>
    <li class="li"><a href="#thread-states">Thread States</a></li>
    <li class="li"><a href="#thread-synchronization">Thread Synchronization</a></li>
    <li class="li"><a href="#thread-communication">Thread Communication</a></li>
    <li class="li"><a href="#thread-pooling">Thread Pooling</a></li>
    <li class="li"><a href="#thread-safety">Thread Safety</a></li>
    <li class="li"><a href="#best-practices-for-multithreading">Best Practices for Multithreading</a></li>
    <li class="li"><a href="#thread-class-hierarchy">Thread Class Hierarchy</a></li>
    <li class="li"><a href="#summary">Summary</a></li>
  </ul>

### What are Threads?
<p id="what-are-threads">
A **thread** is the smallest unit of execution in a program. Threads allow the program to perform multiple tasks simultaneously, enabling more efficient use of system resources. Java supports multithreading, which is essential for applications that need to run multiple tasks concurrently, such as servers, real-time applications, and data processing.
</p>


### Creating Threads
<p id="creating-threads">
Threads in Java can be created in two primary ways:
</p>

1. **Using the `Runnable` Interface**  
2. **Using the `Thread` Class**

#### Using the `Runnable` Interface
<p id="using-the-runnable-interface">
The `Runnable` interface is a more flexible way to create threads. It allows you to define the code to be executed in a separate method (the `run()` method). This is useful if the class already extends another class, as Java allows only single inheritance but implements multiple interfaces.
</p
>

##### Steps to Implement `Runnable`:

1. **Implement the `Runnable` interface.**
2. **Override the `run()` method** to define the task to be executed.
3. **Pass the `Runnable` object to a `Thread` object** and call `start()`.

##### Example of `Runnable` Interface:
<div class="example">
<pre>
class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("Thread is running using Runnable.");
    }
    public static void main(String[] args) {
        MyRunnable myRunnable = new MyRunnable();
        Thread thread = new Thread(myRunnable);
        thread.start(); // Start the thread
    }
}
</pre>
</div>

#### Using the `Thread` Class
<p id="using-the-thread-class">
You can also create a thread by extending the `Thread` class. However, this approach is less flexible because it doesn't allow you to inherit from any other class, as Java supports only single inheritance.
</p>

##### Steps to Extend `Thread`:

1. **Extend the `Thread` class.**
2. **Override the `run()` method** to define the task to be executed.
3. **Call the `start()` method** to begin execution.

##### Example of Extending `Thread` Class:
<div class="example">
<pre>
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Thread is running using the Thread class.");
    }
    public static void main(String[] args) {
        MyThread myThread = new MyThread();
        myThread.start(); // Start the thread
    }
}
</pre>
</div>

### Thread Lifecycle
A thread's lifecycle refers to the various stages it goes through from creation to termination. The primary stages are:

<div align="center" width=500 height=200>
<img src="IMAGES/threadlifecycle.png" class="lifecycle" alt="Java Code Execution" width=500 height=200>
</div>
<br/>

### Thread States
<p id="thread-states">
Threads can be in one of the following states:
</p>

- **New**: The thread has been created but not started.
- **Runnable**: The thread is ready to run and will run when it gets CPU time.
- **Blocked**: The thread is blocked and waiting for a resource (like I/O).
- **Waiting**: The thread is waiting indefinitely for another thread to notify it.
- **Timed Waiting**: The thread is waiting for a specified time (using `sleep()` or `join()`).
- **DEAD/TERMINATED**: The thread has completed its execution.

### Thread Synchronization
<p id="thread-synchronization" >
When multiple threads access shared resources, there is a risk of data inconsistency or corruption due to race conditions. **Synchronization** ensures that only one thread can access a resource at a time.
</p>
#### Synchronized Method
<div class="example">
<pre>
class Counter {
    private int count = 0;
    public synchronized void increment() {
        count++;
    }
    public int getCount() {
        return count;
    }
}
</pre>
</div>

- The `synchronized` keyword ensures that only one thread can access the `increment()` method at a time.

#### Synchronized Block
<div class="example">
<pre>
class Counter {
    private int count = 0;
    public void increment() {
        synchronized(this) {
            count++;
        }
    }
    public int getCount() {
        return count;
    }
}
</pre>
</div>

- A synchronized block provides fine-grained control over which part of the code is synchronized.

### Thread Communication
<p id="thread-communication">
Threads can communicate with each other using `wait()`, `notify()`, and `notifyAll()`. These methods are used for coordinating the execution of threads.
</p>

#### Example of Producer-Consumer Problem:
<div class="example">
<pre>
class ProducerConsumer {
    private int data = 0;
    public synchronized void produce() throws InterruptedException {
        while (data != 0) {
            wait(); // Wait for the consumer to consume the data
        }
        data++;
        System.out.println("Produced: " + data);
        notify(); // Notify the consumer that new data is available
    }
    public synchronized void consume() throws InterruptedException {
        while (data == 0) {
            wait(); // Wait for the producer to produce data
        }
        System.out.println("Consumed: " + data);
        data--;
        notify(); // Notify the producer that space is available
    }
}
</pre>
</div>

### Thread Pooling
<p id="thread-pooling">

**Thread pooling** is a technique where a pool of threads is created and reused to perform tasks. This prevents the overhead of creating new threads each time a task needs to be executed. The `ExecutorService` in Java handles thread pooling.
</p>

#### Example Using `ExecutorService`:
<div class="example">
<pre>
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

class MyTask implements Runnable {
    @Override
    public void run() {
        System.out.println("Task executed by: " + Thread.currentThread().getName());
    }
    public static void main(String[] args) {
        ExecutorService executorService = Executors.newFixedThreadPool(3);
        for (int i = 0; i < 5; i++) {
            executorService.submit(new MyTask());
        }
        executorService.shutdown(); // Gracefully shut down the executor service
    }
}
</pre>
</div>

- The `ExecutorService` creates a fixed number of threads and assigns tasks to them. The pool reuses threads, reducing the overhead of creating new threads each time.

### Thread Safety

<br id="thread-safety">
**Thread safety** ensures that multiple threads can access shared resources concurrently without causing data corruption. This can be achieved through synchronization, atomic variables, and thread-safe collections.

#### Thread-Safe Collections:
- `Vector` and `Hashtable` (less common today).
- `ConcurrentHashMap`, `CopyOnWriteArrayList`, `BlockingQueue`, etc.

#### Example of Atomic Class:
<div class="example">
<pre>
import java.util.concurrent.atomic.AtomicInteger;

class Counter {
    private AtomicInteger count = new AtomicInteger(0);
    public void increment() {
        count.incrementAndGet(); // Atomically increments the value
    }
    public int getCount() {
        return count.get();
    }
}
</pre>
</div>

### Best Practices for Multithreading
<br id="best-practices-for-multithreading">

- **Minimize synchronization**: Synchronizing too much can lead to performance degradation. Only synchronize shared resources.
- **Avoid deadlocks**: Design threads to avoid circular waiting. Use lock ordering and timeouts.
- **Use thread pools**: Thread pools reduce the cost of thread creation and management.

### Thread Class Hierarchy
<br id="thread-class-hierarchy">

Java's thread-related classes are part of a well-defined hierarchy. Here's how they relate:

- **`Thread` Class**
  - Extends: `Object`
  - Implements: `Runnable`
  - The `Thread` class itself provides methods like `start()`, `sleep()`, `join()`, etc., for thread management.

- **`Runnable` Interface**
  - This interface is implemented by classes that represent tasks to be executed by a thread.
  - The `Runnable` interface has a single method: `void run()`, which is executed when the thread starts.

The `Thread` class can either implement the `Runnable` interface or extend the `Thread` class itself.

#### Hierarchy Diagram:
<!-- Diagram placeholder -->

### Summary

<br id="summary">

- **Runnable Interface**: Ideal for creating threads without extending the `Thread` class. It allows more flexibility, especially when your class already extends another class.
- **Thread Lifecycle**: Threads move through various states, including new, runnable, waiting, and terminated.
- **Synchronization**: Ensures thread safety by controlling access to shared resources.
- **Thread Pooling**: Helps manage threads more efficiently using a fixed pool of threads.
- **Thread Safety**: Achieved through synchronization, atomic variables, and thread-safe collections.

Mastering threads and multithreading in Java is essential for writing efficient, concurrent applications.
