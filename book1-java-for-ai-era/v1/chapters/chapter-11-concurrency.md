# Chapter 11: Concurrency - Doing Multiple Things at Once

> "Concurrency is not about doing things faster - it's about doing things smarter. Understanding it prevents bugs that are nearly impossible to debug."

---

## What You'll Learn

- What concurrency is and why it matters
- Threads: creating and managing them
- Thread lifecycle and states
- Synchronization and thread safety
- Common concurrency problems: race conditions, deadlocks
- Modern concurrency tools: ExecutorService, CompletableFuture
- What AI often gets wrong with concurrent code

---

## Why Concurrency?

Modern computers have multiple CPU cores. Without concurrency:
- Your program uses only one core
- Long operations block everything
- Users wait unnecessarily

With concurrency:
- Utilize all available cores
- Keep UI responsive during heavy operations
- Handle multiple requests simultaneously

```java
// Without concurrency - sequential
downloadFile("file1.zip");  // Takes 5 seconds
downloadFile("file2.zip");  // Takes 5 seconds
// Total: 10 seconds

// With concurrency - parallel
// Both downloads start simultaneously
// Total: ~5 seconds
```

---

## Threads: The Basics

A **thread** is a path of execution within a program. Every Java program starts with one thread (the main thread).

### Creating Threads

**Method 1: Extend Thread**

```java
public class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Thread running: " + getName());
    }
}

// Usage
MyThread thread = new MyThread();
thread.start();  // NOT run()!
```

**Method 2: Implement Runnable (Preferred)**

```java
public class MyTask implements Runnable {
    @Override
    public void run() {
        System.out.println("Task running");
    }
}

// Usage
Thread thread = new Thread(new MyTask());
thread.start();

// Or with lambda
Thread thread2 = new Thread(() -> System.out.println("Lambda task"));
thread2.start();
```

### start() vs run()

```java
Thread thread = new Thread(() -> {
    System.out.println("Running in: " + Thread.currentThread().getName());
});

// WRONG - runs in main thread
thread.run();  // Output: Running in: main

// CORRECT - runs in new thread
thread.start();  // Output: Running in: Thread-0
```

### Thread Properties

```java
Thread thread = new Thread(() -> {
    // Thread code
});

// Set properties before starting
thread.setName("WorkerThread");
thread.setPriority(Thread.MAX_PRIORITY);  // 1-10, default 5
thread.setDaemon(true);  // Background thread, won't prevent JVM exit

thread.start();

// Query properties
System.out.println("Name: " + thread.getName());
System.out.println("ID: " + thread.getId());
System.out.println("State: " + thread.getState());
System.out.println("Is Alive: " + thread.isAlive());
```

---

## Thread Lifecycle

```
NEW → RUNNABLE ↔ BLOCKED/WAITING/TIMED_WAITING → TERMINATED
```

| State | Description |
|-------|-------------|
| NEW | Created but not started |
| RUNNABLE | Executing or ready to execute |
| BLOCKED | Waiting for monitor lock |
| WAITING | Waiting indefinitely for another thread |
| TIMED_WAITING | Waiting for specified time |
| TERMINATED | Finished execution |

### Controlling Thread Execution

```java
// Sleep - pause current thread
Thread.sleep(1000);  // Sleep 1 second

// Join - wait for another thread to finish
Thread worker = new Thread(() -> {
    // Long operation
});
worker.start();
worker.join();  // Main thread waits for worker to finish
System.out.println("Worker finished");

// Join with timeout
worker.join(5000);  // Wait max 5 seconds

// Yield - hint to scheduler to let other threads run
Thread.yield();

// Interrupt - request thread to stop
worker.interrupt();

// Check if interrupted
if (Thread.interrupted()) {
    // Handle interruption
}
```

### Handling Interruption

```java
public class InterruptibleTask implements Runnable {
    @Override
    public void run() {
        while (!Thread.currentThread().isInterrupted()) {
            try {
                // Do work
                Thread.sleep(100);
            } catch (InterruptedException e) {
                // Restore interrupt status
                Thread.currentThread().interrupt();
                break;
            }
        }
        System.out.println("Task stopped cleanly");
    }
}
```

---

## Thread Safety Problems

### Race Condition

When multiple threads access shared data without proper synchronization:

```java
public class Counter {
    private int count = 0;

    public void increment() {
        count++;  // NOT atomic! Read → Add → Write
    }

    public int getCount() {
        return count;
    }
}

// Problem demonstration
Counter counter = new Counter();

Thread t1 = new Thread(() -> {
    for (int i = 0; i < 10000; i++) counter.increment();
});
Thread t2 = new Thread(() -> {
    for (int i = 0; i < 10000; i++) counter.increment();
});

t1.start(); t2.start();
t1.join(); t2.join();

System.out.println(counter.getCount());  // Expected: 20000, Actual: random lower value!
```

### Deadlock

When threads wait for each other indefinitely:

```java
Object lock1 = new Object();
Object lock2 = new Object();

// Thread 1: locks lock1, then tries to lock lock2
Thread t1 = new Thread(() -> {
    synchronized (lock1) {
        Thread.sleep(100);
        synchronized (lock2) {
            System.out.println("Thread 1");
        }
    }
});

// Thread 2: locks lock2, then tries to lock lock1
Thread t2 = new Thread(() -> {
    synchronized (lock2) {
        Thread.sleep(100);
        synchronized (lock1) {  // Deadlock!
            System.out.println("Thread 2");
        }
    }
});

// Both threads wait forever!
```

---

## Synchronization

### The synchronized Keyword

**Method level:**

```java
public class SafeCounter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public synchronized int getCount() {
        return count;
    }
}
```

**Block level (finer control):**

```java
public class SafeCounter {
    private int count = 0;
    private final Object lock = new Object();

    public void increment() {
        synchronized (lock) {
            count++;
        }
    }

    public int getCount() {
        synchronized (lock) {
            return count;
        }
    }
}
```

### Volatile Keyword

Ensures visibility of changes across threads (but not atomicity):

```java
public class Flag {
    private volatile boolean running = true;

    public void stop() {
        running = false;  // Immediately visible to other threads
    }

    public void run() {
        while (running) {
            // Do work
        }
    }
}
```

### Atomic Classes

For atomic operations without explicit synchronization:

```java
import java.util.concurrent.atomic.*;

public class AtomicCounter {
    private AtomicInteger count = new AtomicInteger(0);

    public void increment() {
        count.incrementAndGet();  // Atomic!
    }

    public int getCount() {
        return count.get();
    }
}

// Other atomic classes
AtomicLong atomicLong = new AtomicLong();
AtomicBoolean atomicBoolean = new AtomicBoolean();
AtomicReference<String> atomicRef = new AtomicReference<>();

// Operations
int old = count.getAndIncrement();  // Return then increment
int next = count.incrementAndGet();  // Increment then return
boolean success = count.compareAndSet(5, 10);  // CAS operation
```

---

## ExecutorService: Modern Thread Management

Don't create threads manually. Use ExecutorService for better control.

### Creating Executors

```java
import java.util.concurrent.*;

// Fixed thread pool
ExecutorService fixed = Executors.newFixedThreadPool(4);

// Cached thread pool (creates threads as needed)
ExecutorService cached = Executors.newCachedThreadPool();

// Single thread executor (tasks run sequentially)
ExecutorService single = Executors.newSingleThreadExecutor();

// Scheduled executor (for delayed/periodic tasks)
ScheduledExecutorService scheduled = Executors.newScheduledThreadPool(2);
```

### Submitting Tasks

```java
ExecutorService executor = Executors.newFixedThreadPool(4);

// Submit Runnable (no result)
executor.submit(() -> System.out.println("Task 1"));

// Submit Callable (returns result)
Future<Integer> future = executor.submit(() -> {
    Thread.sleep(1000);
    return 42;
});

// Get result (blocks until complete)
Integer result = future.get();  // 42

// Get with timeout
Integer result2 = future.get(5, TimeUnit.SECONDS);

// Check status
boolean isDone = future.isDone();
boolean isCancelled = future.isCancelled();

// Cancel
future.cancel(true);  // true = interrupt if running

// Always shutdown!
executor.shutdown();
executor.awaitTermination(60, TimeUnit.SECONDS);
```

### Bulk Task Submission

```java
ExecutorService executor = Executors.newFixedThreadPool(4);

List<Callable<Integer>> tasks = Arrays.asList(
    () -> { Thread.sleep(1000); return 1; },
    () -> { Thread.sleep(1000); return 2; },
    () -> { Thread.sleep(1000); return 3; }
);

// Execute all, wait for all
List<Future<Integer>> futures = executor.invokeAll(tasks);
for (Future<Integer> f : futures) {
    System.out.println(f.get());
}

// Execute all, return first completed
Integer first = executor.invokeAny(tasks);

executor.shutdown();
```

### Scheduled Execution

```java
ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(2);

// Run once after delay
scheduler.schedule(() -> System.out.println("Delayed"), 5, TimeUnit.SECONDS);

// Run periodically (fixed rate)
scheduler.scheduleAtFixedRate(
    () -> System.out.println("Every 2 seconds"),
    0,   // Initial delay
    2,   // Period
    TimeUnit.SECONDS
);

// Run periodically (fixed delay between end and next start)
scheduler.scheduleWithFixedDelay(
    () -> System.out.println("2 seconds after completion"),
    0,
    2,
    TimeUnit.SECONDS
);
```

---

## CompletableFuture: Async Programming

Modern async programming with composable operations.

### Creating CompletableFutures

```java
import java.util.concurrent.CompletableFuture;

// Run async (no result)
CompletableFuture<Void> cf1 = CompletableFuture.runAsync(() -> {
    System.out.println("Running async");
});

// Supply async (with result)
CompletableFuture<String> cf2 = CompletableFuture.supplyAsync(() -> {
    return "Hello";
});

// Complete manually
CompletableFuture<String> cf3 = new CompletableFuture<>();
cf3.complete("Manual result");

// Already completed
CompletableFuture<String> cf4 = CompletableFuture.completedFuture("Instant");
```

### Chaining Operations

```java
CompletableFuture<String> future = CompletableFuture
    .supplyAsync(() -> "Hello")
    .thenApply(s -> s + " World")      // Transform result
    .thenApply(String::toUpperCase);

System.out.println(future.get());  // "HELLO WORLD"

// thenAccept - consume result, no return
CompletableFuture.supplyAsync(() -> "Hello")
    .thenAccept(System.out::println);

// thenRun - run action, ignore result
CompletableFuture.supplyAsync(() -> "Hello")
    .thenRun(() -> System.out.println("Done"));
```

### Combining Futures

```java
// thenCompose - chain dependent futures (flatMap)
CompletableFuture<String> cf = getUserId()
    .thenCompose(id -> getUser(id))
    .thenCompose(user -> getOrders(user));

// thenCombine - combine two independent futures
CompletableFuture<String> name = getName();
CompletableFuture<Integer> age = getAge();

CompletableFuture<String> combined = name.thenCombine(age,
    (n, a) -> n + " is " + a + " years old");

// allOf - wait for all
CompletableFuture<Void> all = CompletableFuture.allOf(cf1, cf2, cf3);
all.join();  // Wait for all to complete

// anyOf - complete when any completes
CompletableFuture<Object> any = CompletableFuture.anyOf(cf1, cf2, cf3);
Object firstResult = any.join();
```

### Error Handling

```java
CompletableFuture<String> future = CompletableFuture
    .supplyAsync(() -> {
        if (Math.random() > 0.5) {
            throw new RuntimeException("Error!");
        }
        return "Success";
    })
    .exceptionally(ex -> {
        System.out.println("Exception: " + ex.getMessage());
        return "Default";
    });

// handle - process result or exception
CompletableFuture<String> handled = CompletableFuture
    .supplyAsync(() -> {
        // may throw
        return "Result";
    })
    .handle((result, ex) -> {
        if (ex != null) {
            return "Error: " + ex.getMessage();
        }
        return result.toUpperCase();
    });

// whenComplete - like handle but doesn't transform
CompletableFuture<String> cf = CompletableFuture
    .supplyAsync(() -> "Result")
    .whenComplete((result, ex) -> {
        if (ex != null) {
            System.out.println("Failed: " + ex);
        } else {
            System.out.println("Success: " + result);
        }
    });
```

### Async Variations

By default, `thenApply`, `thenAccept`, etc. run in the same thread. Use `*Async` variants to run in executor:

```java
CompletableFuture<String> future = CompletableFuture
    .supplyAsync(() -> "Hello")
    .thenApplyAsync(s -> s + " World")      // Runs in ForkJoinPool
    .thenApplyAsync(s -> s + "!", executor); // Runs in custom executor
```

---

## Thread-Safe Collections

### ConcurrentHashMap

```java
import java.util.concurrent.ConcurrentHashMap;

ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();

// Safe operations
map.put("key", 1);
map.putIfAbsent("key", 2);  // Only if absent
map.computeIfAbsent("key", k -> computeValue(k));

// Atomic update
map.compute("key", (k, v) -> v == null ? 1 : v + 1);

// Parallel operations
map.forEach(4, (k, v) -> System.out.println(k + "=" + v));
int sum = map.reduceValues(4, Integer::sum);
```

### CopyOnWriteArrayList

```java
import java.util.concurrent.CopyOnWriteArrayList;

// Good for read-heavy, write-rare scenarios
CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
list.add("item");

// Safe iteration during modification
for (String item : list) {
    list.add("new");  // Won't cause ConcurrentModificationException
}
```

### BlockingQueue

```java
import java.util.concurrent.*;

BlockingQueue<String> queue = new ArrayBlockingQueue<>(100);

// Producer
queue.put("item");  // Blocks if full

// Consumer
String item = queue.take();  // Blocks if empty

// Non-blocking alternatives
boolean added = queue.offer("item");  // Returns false if full
String polled = queue.poll();  // Returns null if empty

// With timeout
queue.offer("item", 1, TimeUnit.SECONDS);
queue.poll(1, TimeUnit.SECONDS);
```

---

## Hands-On Project: Parallel Web Scraper

```java
import java.util.concurrent.*;
import java.util.*;

public class ParallelScraper {

    private final ExecutorService executor = Executors.newFixedThreadPool(10);

    public Map<String, String> scrapeUrls(List<String> urls) {
        Map<String, String> results = new ConcurrentHashMap<>();

        List<CompletableFuture<Void>> futures = urls.stream()
            .map(url -> CompletableFuture.runAsync(() -> {
                String content = fetchUrl(url);
                results.put(url, content);
            }, executor))
            .toList();

        // Wait for all to complete
        CompletableFuture.allOf(futures.toArray(new CompletableFuture[0])).join();

        return results;
    }

    public List<String> scrapeWithTimeout(List<String> urls, int timeoutSeconds) {
        List<CompletableFuture<String>> futures = urls.stream()
            .map(url -> CompletableFuture
                .supplyAsync(() -> fetchUrl(url), executor)
                .orTimeout(timeoutSeconds, TimeUnit.SECONDS)
                .exceptionally(ex -> "Error: " + ex.getMessage()))
            .toList();

        return futures.stream()
            .map(CompletableFuture::join)
            .toList();
    }

    private String fetchUrl(String url) {
        // Simulate network request
        try {
            Thread.sleep((long) (Math.random() * 2000));
            return "Content from " + url;
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            return "Interrupted";
        }
    }

    public void shutdown() {
        executor.shutdown();
        try {
            if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
                executor.shutdownNow();
            }
        } catch (InterruptedException e) {
            executor.shutdownNow();
        }
    }

    public static void main(String[] args) {
        ParallelScraper scraper = new ParallelScraper();

        List<String> urls = Arrays.asList(
            "https://example.com/page1",
            "https://example.com/page2",
            "https://example.com/page3",
            "https://example.com/page4",
            "https://example.com/page5"
        );

        System.out.println("Starting parallel scrape...");
        long start = System.currentTimeMillis();

        Map<String, String> results = scraper.scrapeUrls(urls);

        long elapsed = System.currentTimeMillis() - start;
        System.out.println("Completed in " + elapsed + "ms");

        results.forEach((url, content) ->
            System.out.println(url + " → " + content.substring(0, Math.min(50, content.length()))));

        scraper.shutdown();
    }
}
```

---

## AI Integration: Concurrency Mistakes

### Problem 1: Not Shutting Down ExecutorService

**AI generates:**
```java
ExecutorService executor = Executors.newFixedThreadPool(4);
executor.submit(() -> doWork());
// ExecutorService never shut down - application won't exit!
```

**Fix:**
```java
ExecutorService executor = Executors.newFixedThreadPool(4);
try {
    executor.submit(() -> doWork());
} finally {
    executor.shutdown();
    executor.awaitTermination(60, TimeUnit.SECONDS);
}
```

### Problem 2: Race Conditions

**AI generates:**
```java
private int counter = 0;

public void increment() {
    counter++;  // Not thread-safe!
}
```

**Fix:**
```java
private AtomicInteger counter = new AtomicInteger(0);

public void increment() {
    counter.incrementAndGet();
}
```

### Problem 3: Calling run() Instead of start()

**AI generates:**
```java
Thread thread = new Thread(() -> doWork());
thread.run();  // WRONG - runs in main thread!
```

**Fix:**
```java
Thread thread = new Thread(() -> doWork());
thread.start();  // Correct - runs in new thread
```

### Problem 4: Ignoring InterruptedException

**AI generates:**
```java
try {
    Thread.sleep(1000);
} catch (InterruptedException e) {
    // Ignored - BAD!
}
```

**Fix:**
```java
try {
    Thread.sleep(1000);
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();  // Restore interrupt status
    return;  // Or handle appropriately
}
```

### Problem 5: Over-synchronization

**AI generates:**
```java
public synchronized void processAll(List<Item> items) {
    for (Item item : items) {
        process(item);  // Locks everything for entire duration!
    }
}
```

**Fix:**
```java
public void processAll(List<Item> items) {
    for (Item item : items) {
        synchronized (item) {  // Lock only what's necessary
            process(item);
        }
    }
}
```

---

## Exercises

### Exercise 11.1: Counter
Create a thread-safe counter class that supports `increment()`, `decrement()`, and `getValue()`. Test with multiple threads.

### Exercise 11.2: Producer-Consumer
Implement a producer-consumer pattern using BlockingQueue. Producer adds numbers, consumer calculates squares.

### Exercise 11.3: Parallel Processing
Write a method that processes a list of files in parallel using ExecutorService, returning results as they complete.

### Exercise 11.4: Async Chain
Use CompletableFuture to chain: fetch user → get orders → calculate total. Handle errors at each step.

### Exercise 11.5: Fix AI Code
Fix the concurrency issues in this AI-generated code:

```java
public class TaskProcessor {
    private List<String> results = new ArrayList<>();
    private ExecutorService executor = Executors.newFixedThreadPool(4);

    public void processTasks(List<String> tasks) {
        for (String task : tasks) {
            executor.submit(() -> {
                String result = process(task);
                results.add(result);
            });
        }
    }

    public List<String> getResults() {
        return results;
    }
}
```

---

## Key Takeaways

- **Use ExecutorService, not raw threads** - Better resource management
- **Always shutdown ExecutorService** - Or your program won't exit
- **Understand race conditions** - Multiple threads + shared state = bugs
- **Use atomic classes for simple cases** - AtomicInteger, AtomicBoolean
- **Use synchronized sparingly** - Over-synchronization hurts performance
- **Handle InterruptedException properly** - Restore interrupt status
- **CompletableFuture for async chains** - Compose async operations cleanly
- **Use concurrent collections** - ConcurrentHashMap, BlockingQueue

---

## What's Next

You understand how to write concurrent code safely. In **Chapter 12**, we'll explore Project Structure and Maven - how to organize larger Java projects and manage dependencies professionally.

---

*"Concurrency bugs are the hardest to find because they don't always happen. Get it right the first time."*
