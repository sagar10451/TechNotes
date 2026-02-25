Your notes are actually very solid and show a strong understanding of the underlying architecture. I have refined the terminology for better technical accuracy and improved the flow, particularly around how JVM memory and thread-specific resources interact.

---

## **Multithreading in Java**

### **1. Process**

* **Definition:** An instance of a computer program that is being executed.
* **Java Context:** When you compile code using `javac`, bytecode is created. When you execute it, the OS starts a new process for the JVM.
* **Isolation:** Each process has its own **independent heap memory**. Processes do not share memory with each other by default.

### **2. Thread**

* **Definition:** Often called a **lightweight process**, a thread is the smallest sequence of programmed instructions that can be managed independently by a scheduler.
* **The Main Thread:** Every Java process starts with at least one thread, the `main` thread. From here, we can spawn multiple threads to perform concurrent tasks.
* **Hierarchy:** One process can contain multiple threads that share the same resources of that process.

---

### **3. Memory Management**

When a JVM instance is created, it allocates memory based on specific parameters:

* **Initial Heap Size ($X_{ms}$):** The memory the JVM starts with.
* **Maximum Heap Size ($X_{mx}$):** The upper limit the JVM can claim. (Typically defaults to 1/4 of physical RAM).

#### **What Threads Share (Process Level):**

* **Code Segment:** Contains the compiled machine code (read-only).
* **Data Segment:** Stores global and **static variables**. Shared across all threads; requires synchronization.
* **Heap:** Stores all objects created with the `new` keyword. Two processes have independent heaps, but all threads within one process share the same heap.

#### **What Threads Keep Private (Thread Level):**

* **Stack:** Manages method calls, return addresses, and **local variables**.
* **Program Counter (PC):** A pointer to the address of the next instruction. This is vital for **Context Switching**, so a thread knows exactly where to resume.
* **Registers:** Holds high-frequency data like temporary variables and memory addresses for immediate CPU processing.

---

### **4. Key Concepts**

* **Context Switching:** The process of the CPU storing the state of a running thread and loading the state of another. This allows multiple threads to share a single CPU core.
* **Multithreading:** Allowing a program to execute multiple paths of code simultaneously to improve responsiveness and performance.
* **Concurrency vs. Parallelism:**
* **Concurrency:** Handling multiple tasks at once (interleaving). Possible on a single-core via context switching.
* **Parallelism:** Executing multiple tasks *simultaneously* on multiple CPU cores.



| Feature | Multi-tasking | Multi-threading |
| --- | --- | --- |
| **Level** | OS / Process level | Within a single process |
| **Memory** | Processes have separate memory | Threads share the same memory (Heap/Data) |
| **Switching** | Heavy overhead | Lower overhead (Lightweight) |

---

### **5. Benefits and Challenges**

* **Benefits:**
* **Task Parallelism:** Faster execution of heavy tasks.
* **Responsiveness:** Keeps the UI/Main thread from freezing during long operations.
* **Resource Sharing:** Threads share memory, making communication easier than between processes.


* **Challenges:**
* **Synchronization Overhead:** Managing shared data can slow down the system if not done carefully.
* **Deadlocks:** Two or more threads waiting for each other indefinitely.
* **Race Conditions:** Inconsistent results when multiple threads modify the same data at once.
* **Complexity:** Testing and debugging multi-threaded code is significantly harder than single-threaded code.


Video-39:

Your notes are excellent and cover the core mechanics of Java threading very well. I have refined some of the technical nuances—specifically the difference between **Blocked** and **Waiting** states and how the **Monitor Lock** behaves at the object level.

---

## **Ways to Create a Thread**

There are two primary ways to define a thread in Java. While both work, they serve different architectural purposes.

### **1. Using the `Runnable` Interface (Preferred)**

* **Why:** Java only supports **single inheritance**. If your class already extends another class (e.g., `BaseService`), it cannot extend `Thread`. Implementing `Runnable` keeps your inheritance "slot" open.
* **Decoupling:** It separates the **task** (the `run()` logic) from the **runner** (the `Thread` object).

**Execution Steps:**

1. Implement `Runnable` and override `run()`.
2. Pass the `Runnable` instance to a `Thread` constructor.
3. Call `start()`.

```java
// Using Lambda (Modern approach)
Runnable task = () -> System.out.println("Running in: " + Thread.currentThread().getName());
Thread th = new Thread(task);
th.start();

```

### **2. Extending the `Thread` Class**

* **Mechanism:** Since your class *is* a `Thread`, you can call `start()` directly on your object.
* **Downside:** It ties your code strictly to the Thread hierarchy and is generally considered less flexible in production environments.

---

## **Thread Life Cycle (States)**

1. **New:** Thread object created but `start()` not yet called.
2. **Runnable:** The thread is ready to run and waiting for the OS thread scheduler to give it CPU time.
3. **Running:** The CPU is actively executing the thread's `run()` method.
4. **Blocked:** The thread is waiting to acquire a **Monitor Lock** that is currently held by another thread.
5. **Waiting:** The thread waits indefinitely for another thread to perform a specific action (e.g., `object.wait()` requires `object.notify()`).
6. **Timed Waiting:** The thread waits for a specific period (e.g., `Thread.sleep(1000)` or `wait(1000)`).
7. **Terminated:** The thread has finished execution or was stopped.

> **Clarification on I/O:** When a thread performs a "Blocking I/O" operation (like reading a large file), it technically enters a state where it's waiting for the OS, but in the JVM, it often remains in the **Runnable** state while waiting for the resource.

---

## **Monitor Locks & Synchronization**

### **The Concept of "One Lock per Object"**

Every object in Java has a built-in **Monitor Lock** (or Intrinsic Lock).

* **Synchronized Method:** If a method is marked `synchronized`, a thread must acquire the lock of that **specific object instance** before it can execute the method.
* **The "Wait" Rule:** If Thread A is executing a synchronized method, Thread B cannot enter *any* synchronized method on that same object because the lock is already taken.
* **Static Synchronization:** If a method is `static synchronized`, the lock is held on the **Class object**, not the instance.

### **Lock Release Behavior**

| State | Releases Monitor Lock? |
| --- | --- |
| **`wait()`** | **Yes.** It must release the lock so other threads can enter and eventually call `notify()`. |
| **`Thread.sleep()`** | **No.** It keeps the lock while sleeping, which can lead to efficiency issues. |
| **Blocked** | **N/A.** The thread hasn't acquired the lock yet, so it has nothing to release. |

### **Summary of Synchronization**

* **Synchronized Block:** More granular. You can lock on a specific object (`synchronized(this) { ... }`) or a private lock object. This is usually preferred over synchronized methods to reduce the "critical section" size.
* **Goal:** To prevent **Race Conditions** by ensuring only one thread can access the critical section of code at a time.

---

Video-40:

Your notes on the **Producer-Consumer** problem and **Thread Join** are clear and conceptually sound. I have refined the section on deprecated methods to explain *why* they were dangerous and polished the explanation of `join()` to show how it fits into the thread lifecycle.

---

## **The Producer-Consumer Problem**

This is a classic multi-threading scenario where two threads must coordinate their actions based on a shared buffer (usually a **Queue**).

### **The Workflow**

* **Producer:** Adds data to the queue. If the queue is **Full**, the producer must enter a **Waiting** state to avoid overflow.
* **Consumer:** Removes data from the queue. If the queue is **Empty**, the consumer must enter a **Waiting** state to avoid underflow.
* **Coordination:** This requires `wait()` and `notify()`/`notifyAll()`. When a producer adds an item, it notifies the consumer; when a consumer removes an item, it notifies the producer.

---

## **Deprecated Methods: Why We Don't Use Them**

Java originally had `stop()`, `suspend()`, and `resume()`, but they are now **Deprecated** because they are inherently unsafe.

### **1. `stop()**`

* **The Risk:** It terminates a thread abruptly.
* **The Consequence:** The thread might be in the middle of updating a shared data structure. If it stops suddenly, it **does not release resources** properly and leaves the shared data in an **inconsistent state**.

### **2. `suspend()` & `resume()**`

* **The Risk:** `suspend()` pauses a thread **without releasing its locks**.
* **The Consequence:** If the thread holding a lock is suspended, and the thread responsible for calling `resume()` needs that same lock to proceed, you hit a **Deadlock**. The system freezes because the suspended thread is waiting to be resumed, but the "resumer" is waiting for the lock.

> **Modern Alternative:** Instead of these, we use **flags** (like `volatile boolean isRunning`) or **interrupts** to gracefully tell a thread to stop or pause.

---

## **Thread Join**

The `join()` method is used when one thread needs to wait for the completion of another thread before it can continue its own execution.

### **Scenario: Main Thread and Worker**

1. The **Main Thread** starts **Thread-1**.
2. If the Main Thread finishes its code first, it will terminate, even if Thread-1 is still working.
3. By calling `Thread-1.join()`, the Main Thread effectively says: *"I will stop here and wait for Thread-1 to reach the 'Terminated' state before I run my next line of code."*

### **Why use it?**

* Ensures data initialized in a background thread is ready before the main program uses it.
* Helps in aggregating results from multiple parallel tasks.

---

### **Refined Summary Table**

| Method | Purpose | Lock Behavior | Status |
| --- | --- | --- | --- |
| **`wait()`** | Waits for a condition change. | **Releases** the lock. | Recommended |
| **`join()`** | Waits for a thread to finish. | Does not release other locks. | Recommended |
| **`suspend()`** | Pauses the thread. | **Holds** the lock (Dangerous!). | **Deprecated** |
| **`stop()`** | Kills the thread. | Abrupt exit (Unsafe!). | **Deprecated** |

Video-41:

Your notes on **Thread Priority** and **Daemon Threads** are accurate. I have refined the terminology (correcting "Demon" to "Daemon") and clarified how the JVM interacts with these concepts in a production environment.

---

## **Thread Priority**

Each thread in Java has a priority, which is used by the **Thread Scheduler** to decide which thread should run next. However, in modern Java development, we rarely rely on this.

### **Priority Levels**

* **Scale:** 1 to 10.
* **`Thread.MIN_PRIORITY`:** 1
* **`Thread.NORM_PRIORITY`:** 5 (The default for the main thread)
* **`Thread.MAX_PRIORITY`:** 10

### **The Reality of Priority**

* **Non-Deterministic:** Even if you set a thread to priority 10, the JVM and the underlying Operating System (OS) do not guarantee it will run first. The OS has the final say in scheduling.
* **Platform Dependent:** Different Operating Systems handle priorities differently.
* **Inheritance:** A new thread inherits the priority of the thread that created it (the "parent" thread).

> **Verdict:** Because behavior varies across different machines and OS versions, **Thread Priority is rarely used** in professional production code.

---

## **Daemon Threads**

In Java, threads are classified into two categories: **User Threads** and **Daemon Threads**.

### **What is a Daemon Thread?**

A Daemon thread is a low-priority thread that runs in the background to provide services to user threads.

* **Dependency:** A Daemon thread's life is tied to User threads.
* **Termination Rule:** The JVM will shut down automatically as soon as **all User threads** have finished their execution, even if Daemon threads are still running. The JVM does not wait for Daemons to finish.

### **How to Create One**

You must mark a thread as a daemon **before** you call `start()`.

```java
Thread th1 = new Thread(() -> {
    while(true) {
        System.out.println("Background logging...");
    }
});

th1.setDaemon(true); // Must be called before start()
th1.start();

```

### **Common Use Cases**

* **Garbage Collection (GC):** The JVM's built-in daemon that cleans up memory.
* **Background Tasks:** Features like "Auto-save" in editors or background "Spelling Checkers."
* **Monitoring/Logging:** Systems that collect health metrics while the main application is running.

---

## **Summary Table**

| Feature | User Thread | Daemon Thread |
| --- | --- | --- |
| **Purpose** | To execute the main application logic. | To provide background support services. |
| **JVM Shutdown** | JVM waits for all User threads to finish. | JVM terminates Daemons as soon as all User threads are gone. |
| **Priority** | Typically higher or normal. | Usually lower. |
| **Example** | `main` thread, custom worker threads. | Garbage Collector, Finalizer. |

---

Video-42:

Video-43:

Video-44:

Video-45:

Video-46:

Video-47:

Video-48:

Your notes correctly bridge the gap between basic threading and the more advanced **Executor Framework**. I've refined the technical details regarding how the `Future` object acts as a "placeholder" and the specific differences between `Runnable` and `Callable`.

---

## **The Main Thread & Task Delegation**

* **The Entry Point:** Every Java application begins with the **Main Thread**.
* **The Coordinator:** Even if the main method finishes, the JVM stays alive as long as there are active **User Threads**. The Main thread acts as the initial coordinator, spawning worker threads to handle background tasks.

---

## **The Future Interface**

When you move from manual threads (`new Thread().start()`) to a **ThreadPoolExecutor**, you no longer just "fire and forget." You need a way to track the task.

**Definition:** A `Future` represents the **pending result** of an asynchronous computation. It is a promise that an eventual result (or an exception) will be available.

### **Core Capabilities of Future:**

1. **`isDone()`**: Returns `true` if the task completed (successfully, via exception, or cancellation).
2. **`get()`**: **Blocks** the calling thread (e.g., the Main thread) until the result is ready.
3. **`cancel()`**: Attempts to stop the task execution.
4. **Exception Handling**: If the worker thread throws an exception, `Future.get()` will wrap it in an `ExecutionException`, allowing the Main thread to handle it.

---

## **Runnable vs. Callable**

Both are functional interfaces used to define a task, but they differ in their return capabilities.

| Feature | `Runnable` | `Callable<V>` |
| --- | --- | --- |
| **Method** | `public void run()` | `public V call() throws Exception` |
| **Return Value** | No (Returns `void`) | **Yes** (Returns type `V`) |
| **Exception** | Only Unchecked (Runtime) | Can throw **Checked Exceptions** |
| **Since** | Java 1.0 | Java 1.5 |

### **Why use `submit(Runnable)` if it returns nothing?**

Even though a `Runnable` has no return value, the `ExecutorService` still returns a `Future<?>` (internally `Future<Void>`).

* **Purpose:** You use this `Future` to call `.get()`. If the task finishes successfully, `get()` returns `null`. If the task crashed, `get()` throws an exception. This is how the Main thread knows the background task **failed**.

---

## **The Three Types of Submit**

1. **`submit(Runnable task)`**: Returns `Future<?>`. Used to check if the task is finished or failed.
2. **`submit(Runnable task, T result)`**: Returns `Future<T>`. This is less common; it returns the provided `result` upon completion.
3. **`submit(Callable<T> task)`**: Returns `Future<T>`. This is the most powerful version, used when you actually need a calculated value back from the thread.

---


Video-49:

Your notes on `CompletableFuture` capture the transition from blocking to non-blocking (callback-based) programming. I have clarified the distinction between synchronous and asynchronous chaining, as that is the most critical part of the Java 8 concurrency model.

---

## **CompletableFuture (Java 8)**

`CompletableFuture` is an advanced implementation of the `Future` interface. It solves the biggest limitation of the original `Future`: the need to call a blocking `.get()` to see a result.

### **1. The Executor (Who runs the code?)**

* **Default:** If you don't provide an `Executor`, it uses the **ForkJoinPool.commonPool()**.
* **Custom:** You can pass your own `Executor` (Thread Pool) to methods like `supplyAsync` to control exactly which thread pool is used.

### **2. Starting the Task: `supplyAsync**`

* **Purpose:** To initiate an asynchronous operation.
* **Mechanism:** It takes a `Supplier<T>` and returns a `CompletableFuture<T>`. This task starts immediately in a background thread.

---

## **3. Chaining: `thenApply` vs. `thenApplyAsync**`

This is known as **Callback Chaining**. Instead of waiting for a result, you tell the program: *"When the data is ready, perform this next step."*

#### **`thenApply` (Synchronous Chaining)**

* **Thread Choice:** Typically runs in the **same thread** that finished the previous task.
* **Behavior:** It is "synchronous" relative to the completion of the previous stage. If Task A finishes, Task B starts immediately in that same thread.

#### **`thenApplyAsync` (Asynchronous Chaining)**

* **Thread Choice:** It grabs a **new thread** from the (default or custom) executor.
* **Behavior:** Even if Task A finishes, Task B is submitted as a new task to the thread pool. This is useful if Task B is very heavy and you don't want to block the thread that just finished Task A.

---

## **Key Differences Summary**

| Method | Functional Interface | Returns | Purpose |
| --- | --- | --- | --- |
| **`supplyAsync`** | `Supplier<T>` | `CompletableFuture<T>` | Starts an async task (produces a value). |
| **`thenApply`** | `Function<T, R>` | `CompletableFuture<R>` | Transforms the result (uses same thread). |
| **`thenAccept`** | `Consumer<T>` | `CompletableFuture<Void>` | Consumes the result (no return value). |



Video-50:

Your notes on the `Executors` factory methods are great. I’ve refined the technical specifications (like how the **SynchronousQueue** works in the Cached pool) and clarified the trade-offs for each.

---

## **The `Executors` Utility Class**

While you can create a `ThreadPoolExecutor` manually, Java provides the `Executors` factory class to create pre-configured pools for common use cases.

### **1. Fixed Thread Pool**

* **Method:** `Executors.newFixedThreadPool(n)`
* **Configuration:** * **Core Size == Max Size:** Both are equal to `n`.
* **Queue:** Uses an **Unbounded Queue** (`LinkedBlockingQueue`).


* **When to Use:** When you want to limit the number of threads to a specific, constant number to prevent resource exhaustion.
* **Disadvantage:** Because the queue is unbounded, if tasks come in faster than they are processed, the queue will grow indefinitely, potentially leading to an `OutOfMemoryError`.

---

### **2. Cached Thread Pool**

* **Method:** `Executors.newCachedThreadPool()`
* **Configuration:**
* **Core Size:** 0.
* **Max Size:** `Integer.MAX_VALUE` (essentially unlimited).
* **Queue:** Uses a **SynchronousQueue** (size 0). This queue doesn't "hold" tasks; it handsoff a task immediately to a thread.
* **Keep-Alive:** 60 seconds (unused threads are killed after a minute).


* **When to Use:** Great for many short-lived tasks or "bursty" traffic. It reuses idle threads if available; otherwise, it creates new ones.
* **Disadvantage:** If you submit thousands of long-running tasks, it will try to create thousands of threads, which can crash the JVM due to memory or CPU limits.

---

### **3. Single Thread Executor**

* **Method:** `Executors.newSingleThreadExecutor()`
* **Configuration:**
* **Core Size == Max Size:** 1.
* **Queue:** Unbounded.


* **When to Use:** When you absolutely need tasks to run **sequentially** (one after another) in the order they were submitted.
* **Disadvantage:** Zero concurrency. If one task hangs or takes a long time, all other tasks in the queue are blocked.

---

### **Summary of Factory Methods**

| Pool Type | Core / Max Threads | Queue Type | Best For... |
| --- | --- | --- | --- |
| **Fixed** | Fixed / Fixed | Linked (Unbounded) | Predictable, steady load |
| **Cached** | 0 / Unlimited | Synchronous (Handoff) | Short-lived, spikey tasks |
| **Single** | 1 / 1 | Linked (Unbounded) | Sequential execution |



Video-51:

Video-52:

Your notes on the **Executor Lifecycle** and **Scheduled Thread Pools** are excellent. I have refined the technical nuances—particularly the difference between "fixed rate" and "fixed delay," as that is a common point of confusion.

---

## **Executor Service Lifecycle**

Managing the shutdown of a thread pool is critical to prevent resource leaks and ensure that your application closes gracefully.

### **1. `shutdown()**`

* **Behavior:** Initiates a "soft" shutdown.
* **New Tasks:** The executor will **reject** any new task submissions (throwing a `RejectedExecutionException`).
* **Existing Tasks:** Tasks already in the queue or currently running will be allowed to **finish**.

### **2. `shutdownNow()**`

* **Behavior:** Initiates a "hard" shutdown.
* **New Tasks:** Rejects all new submissions.
* **Existing Tasks:** Attempts to stop currently running tasks (via `thread.interrupt()`) and **removes** all waiting tasks from the queue.
* **Return Value:** It returns a `List<Runnable>` containing the tasks that were in the queue but never got started.

### **3. `awaitTermination(long timeout, TimeUnit unit)**`

* **Behavior:** This is a **blocking** call. The thread calling this (usually the Main thread) will stop and wait.
* **Outcome:** * Returns `true` if the executor fully terminated within the timeout.
* Returns `false` if the timeout reached before all tasks finished.


* **Usage Pattern:** Usually called *after* `shutdown()` to give the pool a window of time to wrap up before the main program exits.

---

## **Scheduled Thread Pool Executor**

This is a specialized pool designed for tasks that need to run in the future or repeatedly.

### **Key Methods**

| Method | Behavior |
| --- | --- |
| **`schedule(task, delay)`** | Runs the task **once** after the specified delay. Supports both `Runnable` and `Callable`. |
| **`scheduleAtFixedRate`** | Runs the task repeatedly. The period is calculated from the **start** of the first execution. If the task takes longer than the period, the next execution starts immediately (tasks won't overlap). |
| **`scheduleWithFixedDelay`** | Runs the task repeatedly. The delay begins **after** the current task completes. This ensures a constant gap between executions. |

### **Important Notes**

* **Infinite Loop:** Unlike a standard executor, these tasks don't stop on their own. You must capture the `ScheduledFuture` and call `future.cancel(true)` to stop the repetition.
* **Exception Handling:** If a scheduled task throws an exception and it isn't caught, the task will **stop running** in the future. Always wrap your logic in a `try-catch` block.

---

### **Refinement Note**

In your notes, you mentioned `scheduleAtFixedRate` for repeated execution. It’s worth noting that Java also has `scheduleWithFixedDelay`.

* **Fixed Rate:** 1:00 PM, 1:05 PM, 1:10 PM (Strict schedule).
* **Fixed Delay:** Task ends at 1:02 PM + 5 min delay = Next starts at 1:07 PM (Strict gap).

**Next Step:** You've covered the core of the Java Concurrency utilities! Would you like to move into **Atomic Variables** (like `AtomicInteger`) and how they provide thread-safety without using `synchronized`?

Video-53:

Your notes on **ThreadLocal** and **Virtual Threads** are excellent and capture the modern evolution of Java concurrency. I have refined the technical explanation of how `ThreadLocal` storage works and clarified the relationship between Virtual Threads and the OS.

---

## **ThreadLocal**

**ThreadLocal** is used to store data that is specific to a single thread. It allows you to define variables that can only be read and written by the same thread.

### **How it Works Internally**

* **The "Map" Concept:** Each `Thread` object actually holds a reference to a `ThreadLocalMap`.
* **The Shared Object:** You create one static `ThreadLocal` instance. When Thread A calls `threadLocal.set("Value A")`, it stores "Value A" in *its own* internal map using the `ThreadLocal` instance as the key.
* **Isolation:** When Thread B calls `threadLocal.get()`, it looks in its own map. It cannot see Thread A's data.

### **Usage and the "Pool" Warning**

* **Use Cases:** Storing User Sessions (SecurityContext), Transaction IDs, or Database Connections in a multi-threaded environment.
* **The Memory Leak Risk:** Because threads in a **Thread Pool** are reused, the `ThreadLocal` variables stay in memory unless explicitly removed.
* **The Fix:** Always call `threadLocal.remove()` in a `finally` block after the task is done.

---

## **Platform Threads vs. Virtual Threads**

This is the biggest change to Java concurrency in years (introduced via Project Loom).

### **1. Platform Threads (Traditional)**

* **Mapping:** 1:1 relationship with Operating System (OS) threads.
* **Cost:** Expensive to create (requires a system call) and memory-heavy (each stack is ~1MB).
* **The I/O Problem:** When a platform thread performs an I/O task (database query, API call), the OS thread is "pinned" and blocked. It sits idle, wasting CPU resources.

### **2. Virtual Threads (Java 19/21+)**

* **Mapping:** M:N relationship. Thousands (or millions) of Virtual Threads can run on a small number of OS threads (called "Carrier Threads").
* **Managed by JVM:** The JVM handles the scheduling, not the OS.
* **The I/O Solution (Unmounting):** When a Virtual Thread hits a blocking I/O operation, the JVM **unmounts** it from the OS Carrier Thread. The OS thread is now free to run a different Virtual Thread. Once the I/O is done, the JVM **remounts** the Virtual Thread to a carrier thread to finish.

| Feature | Platform Thread | Virtual Thread |
| --- | --- | --- |
| **Creation Cost** | High (System Call) | Very Low (JVM Object) |
| **Memory** | ~1MB per thread | A few Kilobytes |
| **Scalability** | Limited by OS (thousands) | Scalable to millions |
| **Best For** | CPU-intensive tasks | I/O-intensive tasks |

### **Refinement Note**

In your notes, you mentioned **Throughput**. Virtual threads don't necessarily make a single task *faster*; they increase the **throughput** of the entire system by allowing the server to handle millions of concurrent requests without running out of threads.

---

### **Summary Table: The Evolution of Concurrency**

* **Java 1.0:** Basic `Thread` and `Runnable`.
* **Java 5:** `ExecutorService` and Thread Pools (to solve Platform Thread creation costs).
* **Java 8:** `CompletableFuture` (to solve the I/O blocking problem using callbacks).
* **Java 21:** **Virtual Threads** (to solve the I/O blocking problem while keeping the code simple and sequential).

**Next Step:** You've completed a comprehensive deep dive into Java Multithreading! Would you like me to compile these into a **final Cheat Sheet** or generate some **interview-style questions** based on these topics?
