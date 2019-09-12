---
layout: default
title:  "Concurrency"
---

# Concurrency
{:.no_toc}

* TOC
{:toc}

## Atomic Variables
- Found in the [java.util.concurrent.atomic](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/atomic/compact1-package-summary.html) package.
- Provides thread safe variables that uses the _Compare and Set_ feature.

### AtomicInteger Example
The following example is not thread safe:

```java
class Counter {
    int count;
    void increment() {
        count++; // Not atomic, not threadsafe.
    }
}

Counter counter = new Counter();
Thread a = new Thread(() -> {
    for (int i = 0; i < 1000; i++) {
        counter.increment();
    }
});
Thread b = new Thread(() -> {
    for (int i = 0; i < 1000; i++) {
        counter.increment();
    }
});

a.start();
b.start();

try {
    a.join();
    b.join();
} catch (InterruptedException e) {}

counter.count // Values like 1735, not guaranteed to be 2000
```

Using an `AtomicInteger` instead of an `int` as follows will ensure `2000` is printed everytime.

```java
import java.util.concurrent.atomic.AtomicInteger;

class Counter {
    AtomicInteger count = new AtomicInteger();
    void increment() {
        count.incrementAndGet();
    }
}
```

## Lock
- Found in the [java.util.concurrent.locks](https://docs.oracle.com/javase/8/docs/api/index.html?java/util/concurrent/atomic/compact1-package-summary.html) package.
- Provides an alternative to `synchronized`.

### ReentrantLock
#### Thread Safe Example Using `synchronized`
```java
class Counter {
    int count;

    synchronized void increment() {
        count++;
    }
}

Counter counter = new Counter();

Runnable counterIncrementer = () -> {
    for (int i = 0; i < 1000; i++) {
        counter.increment();
    }
};

new Thread(counterIncrementer).start();
new Thread(counterIncrementer).start();

try {
    Thread.sleep(10);
} catch (InterruptedException ignored){}

System.out.println(counter.count); // 2000
``` 
#### Thread Safe Example Using `ReentrantLock`
```java
class Counter {
    int count;

    Lock lock = new ReentrantLock();

    void increment() {
        lock.lock();
        count++;
        lock.unlock();
    }
}
```
### Avoiding DeadLock with `tryLock`
```java
import java.util.concurrent.locks.*;

class Counter {
    int count;

    void increment() {
        count++;
    }
}

Counter counter = new Counter();

Lock a = new ReentrantLock();
Lock b = new ReentrantLock();

Runnable r = () -> {
    for (int i = 0; i < 1000;) {
        boolean lockA = a.tryLock();
        boolean lockB = b.tryLock();
        if (lockA && lockB) {
            i++; // only increment if both locks acquired!
            counter.increment();
            a.unlock();
            b.unlock();
        } else {
            if ((lockA && !lockB) || (lockB && !lockA)) {
                System.out.println("Acquired one lock but not the other!");
            }
            if (lockA) {
                a.unlock();
            }
            if (lockB) {
                b.unlock();
            }
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {}
        }
    }
};

Thread first = new Thread(r);
Thread second = new Thread(r);

first.start();
second.start();

try {
    first.join();
    second.join();
} catch (InterruptedException ignored){}

System.out.println(counter.count);

// Acquired one lock but not the other!
// Acquired one lock but not the other!
// Acquired one lock but not the other!
// Acquired one lock but not the other!
// 2000
```
## Condition
- Found in [java.util.concurrent.locks.Condition](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/locks/Condition.html).
- Provides and alternative to `wait-notify`.
- `Lock` of the `Condition` must be locked before `await` is called.
- `InterruptedException` must be handled while awaiting on a `Condition`.
- `Lock` of the `Condition` must be unlocked afterwards.
- `Lock` of the `Condition` must be locked before `signalAll` is called.
- `Lock` of the `Condition` must be unlocked after `signalAll`.

### Condition Example

```java
Lock lock = new ReentrantLock();
Condition condition = lock.newCondition();

new Thread(() -> {
    lock.lock();
    try {
        condition.await();
    } catch (InterruptedException e) {}
    System.out.println("Notified!");
    lock.unlock();
}).start();

try {
    Thread.sleep(500);
} catch (InterruptedException e) {}

System.out.print("Press ⏎ to continue.");
new Scanner(System.in).nextLine();
lock.lock();
condition.signalAll();
lock.unlock();
```

## Executor and ExecutorService

An [Executor](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Executor.html) is used to execute a [Runnable](https://docs.oracle.com/javase/8/docs/api/java/lang/Runnable.html) just like a Thread. Different implementations handle executions in different ways with possibilities such as:
- Not starting a new thread
- Starting a brand new thread for each task
- Running the task in a separate but a thread existing in a pool
[Executors](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Executors.html) is a factory class for returning built-in `Executor`s. The `Executor`s returned by `Executors` are of type [ExecutorService](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ExecutorService.html) which extends `Executor`.

### Different Types of ExecutorServices
- SingleThreadExecutor: Executes all tasks one by one in a single (but separate, not in main) thread.
- FixedThreadPool: Assigns tasks to threads from the pool. This is the most common ExecutorService.
- CachedThreadPool: Creates new threads as needed and removes them from the pool when they are idle for more than 60 seconds. This thread pool can spawn too many threads.

```java
void tryService(ExecutorService es) throws InterruptedException {
    long start = System.currentTimeMillis();
    AtomicInteger atomicInteger = new AtomicInteger(0);

    Runnable zzz = () -> {
        System.out.print(atomicInteger.incrementAndGet() + " ");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException ignored) {}
    };

    for (int i = 0; i < 16; i++) {
        es.execute(zzz);
    }

    es.shutdown();
    es.awaitTermination(2, TimeUnit.MINUTES);

    long end = System.currentTimeMillis();
    System.out.println("\nTook: " + (end - start) + " milliseconds");
}

tryService(Executors.newSingleThreadExecutor());
tryService(Executors.newFixedThreadPool(4));
tryService(Executors.newCachedThreadPool());
```

Output resembles the following:
```plaintext
1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 
Took: 16135 milliseconds
1 2 3 4 5 8 7 6 9 10 11 12 13 14 16 15 
Took: 4023 milliseconds
1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 
Took: 1007 milliseconds
```
__Observations__
SingleThreadExecutor takes the longest time: The values appear one by one with roughly 1 second intervals in the console. FixedThreadPool spawns 4 threads to which all tasks are assigned to. The values appear in groups of 4 in console with roughly 1 second intervals between each group. CachedThreadPool is basically spawning a new thread for every task, which causes all the numbers appear at once in the console.

### ScheduledExecutorService
`ScheduledExecutorService` is Used to schedule tasks to run repeatedly in a given interval.

```java
Runnable r = () -> {
    System.out.println("Sleeping: " + LocalTime.now().format(DateTimeFormatter.ofPattern("mm:ss")));
    try {
        Thread.sleep(1000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    System.out.println("Woke up: " + LocalTime.now().format(DateTimeFormatter.ofPattern("mm:ss")));
};
ScheduledExecutorService es = Executors.newScheduledThreadPool(1);
es.scheduleAtFixedRate(r, 0, 5, TimeUnit.SECONDS);
```

Output

```plaintext
Sleeping: 25:58
Woke up: 25:59
Sleeping: 26:03
Woke up: 26:04
Sleeping: 26:08
Woke up: 26:09
```

## Callable and Future
- Found in [java.util.concurrent.Callable](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Callable.html)
- Just like a Runnable
- Lets you return a result inside wrapped in a [java.util.concurrent.Future](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Future.html)

```java
Callable<Integer> rng = () -> {
    // Heads Up! No need to handle InterruptedException here
    // Callable.call already declares `throws Exception`
    Thread.sleep(1000); // Long running task..
    return ThreadLocalRandom.current().nextInt(0, 10);
};

List<Future<Integer>> futures = new ArrayList<>();
ExecutorService executorService = Executors.newFixedThreadPool(4);
for (int i = 0; i < 16; i++) {
    futures.add(executorService.submit(rng));  // not execute, submit!
}

for (Future future : futures) {
    try {
        // Future.get blocks until the value is ready
        System.out.printf("%s ", future.get());
    } catch (InterruptedException | ExecutionException e) {
        e.printStackTrace();
    }
}

executorService.shutdown();
```

### Cancelling a Running Callable using Future

```java
Callable<Integer> callable = () -> {
    try {
        System.out.println("Started!");
        Thread.sleep(1000);
        System.out.println("Completed!");
        return 42;
    } catch (InterruptedException e) {
        System.out.println("Ooops!");
        return -1;
    }
};

ExecutorService executorService = Executors.newSingleThreadExecutor();
Future<Integer> future = executorService.submit(callable);
Thread.sleep(10);           // Make sure execution of callable starts
future.cancel(true);        // Attempt to interrupt the thread if it started
executorService.shutdown();
// Started!
// Ooops!
```

## Fork-Join Framework
### Important Classes
- [ForkJoinPool](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ForkJoinPool.html)
  - Implements [java.util.concurrent.ExecutorService](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ExecutorService.html)
  - Runs `ForkJoinTask`s
- [ForkJoinTask](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ForkJoinTask.html)
  - Abstract class
- [RecursiveTask](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/RecursiveTask.html)
  - Extends `ForkJoinTask`
  - Returns a value
- [RecursiveAction](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/RecursiveAction.html)
  - Extends `ForkJoinTask`
  - Does not return a value

### Quick Start Guide
- Create a ForkJoinPool by `ForkJoinPool.commonPool`
- Extend either of `RecursiveTask` or `RecursiveAction`
- Run the `ForkJoinTask` by `ForkJoinPool.invoke`

### Example with RecursiveAction
```java
class MyRecursiveAction extends RecursiveAction {
    
    int start;
    int end; 

    public MyRecursiveAction(int start, int end) {
        this.start = start;
        this.end = end;
    }

    public void compute() {
        if (end - start > 500) {
            System.out.println("start:" + start + "\nend: " + end + "\nFork needed!\n");
            int mid = start + ((end - start) / 2);
            MyRecursiveAction left = new MyRecursiveAction(start, mid);
            MyRecursiveAction right = new MyRecursiveAction(mid, end);

            // left.fork();
            // right.compute();
            // left.join();

            // Alternative to above, exactly same behavior:
            invokeAll(left, right);
        } else {
            System.out.println("Computing for: " + start + " " + end);
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {}
        }
    }
}

ForkJoinPool fjp = ForkJoinPool.commonPool();
fjp.invoke(new MyRecursiveAction(0, 2000));

try {
    fjp.awaitTermination(5, TimeUnit.MINUTES);    
} catch (InterruptedException e) {}
fjp.shutdown(); 
``` 
__Output__
```text
start:0
end: 2000
Fork needed!

start:0
end: 1000
Fork needed!

Computing for: 0 500
start:1000
end: 2000
Fork needed!

Computing for: 500 1000
Computing for: 1500 2000
Computing for: 1000 1500
```

### Example with RecursiveTask
```java
import java.util.concurrent.*;

class MyRecursiveTask extends RecursiveTask<Long> {
    static final int[] randomInts = new int[500_000];
    static {
        for (int i = 0; i < 500_000; i++) {
            randomInts[i] = ThreadLocalRandom.current().nextInt(0, 2);
        }
    }

    public MyRecursiveTask() {}
    public MyRecursiveTask(int i, int j) {
        this.i = i;
        this.j = j;
    }

    int i = 0;
    int j = randomInts.length - 1;

    public Long compute() {
        if (j - i > 1000) {
            // This is not a task I can handle..
            int mid = i + (j - i) / 2;
            MyRecursiveTask left =  new MyRecursiveTask(i, mid);
            MyRecursiveTask right =  new MyRecursiveTask(mid, j);
            left.fork();
            long computeRight = right.compute();
            long joinLeft = left.join();
            return computeRight + joinLeft;
        } else {
            // This is a task I can handle
            long sum = 0;
            for (int cursor = i; i < j; i++) {
                sum = sum + randomInts[cursor];
            }
            return sum;
        }
    }
}

ForkJoinPool fjp = ForkJoinPool.commonPool();
long sum = fjp.invoke(new MyRecursiveTask());  // invoke is blocking, execute is non-blocking
// fjp.shutdown();  No need to shutdown the common pool
```
### Things to Remember
- `fork` early, `join` late
- Never `fork` twice, always `fork` and `compute`
- Remember that calling `join` blocks
- Order is important: `fork`, `compute`, `join`
- You can always simply call `invokeAll` on the 2 `RecursiveTask`s, see the example [here](https://docs.oracle.com/javase/tutorial/essential/concurrency/forkjoin.html)

## Examples

### Concurrent HTTP Requests Example
- Node Server
  - Simulates a long running server request
  - Sends a count value in 2.5 seconds to a GET request
  - Tells the client to close the connection

```javascript
const http = require('http');

const port = 4000;
let counter = 0;

const server = http.createServer(function(request, response) {
  response.writeHead(200, {'Connection': 'close'});
  setTimeout(function() {
    counter = counter + 1;
    response.end("" + counter);
  }, 2500);
});

server.listen(port);
```

- Initial Approach
  - Simply makes 10 requests in a basic loop
  - Takes a little more than 25 seconds

```java
import org.apache.http.client.ResponseHandler;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

import java.io.IOException;

public class Main {
    public static void main(String[] args) {
        long start = System.currentTimeMillis();
        for (int i = 0; i < 10; i++) {
            System.out.println(getServerCount());
        }
        long end = System.currentTimeMillis();
        System.out.println("Took " + (end - start) + " milliseconds..");
    }

    private static String getServerCount() {
        try (CloseableHttpClient client = HttpClients.createDefault()) {
            HttpGet httpget = new HttpGet("http://localhost:4000");
            ResponseHandler<String> responseHandler = response -> EntityUtils.toString(response.getEntity());
            return client.execute(httpget, responseHandler);
        } catch (IOException ignored) {
        }
        throw new RuntimeException("Request Failed!");
    }
}
```

- Concurrent Approach
  - Uses a Thread Pool with 10 threads to send requests concurrently
  - Takes a little more than 2.5 seconds to complete

```java
import org.apache.http.client.ResponseHandler;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

import java.io.IOException;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.*;

public class Main {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        long start = System.currentTimeMillis();

        ExecutorService executorService = Executors.newFixedThreadPool(10);
        List<Future<String>> futures = new ArrayList<>();
        for (int i = 0; i < 10; i++) {
            futures.add(executorService.submit(new MyServerCountGetter()));
        }
        executorService.shutdown();

        for (Future<String> stringFuture : futures) {
            String s = stringFuture.get();
            System.out.println(s);
        }

        long end = System.currentTimeMillis();
        System.out.println("Took " + (end - start) + " milliseconds..");
    }
}

class MyServerCountGetter implements Callable<String> {
    @Override
    public String call() {
        try (CloseableHttpClient client = HttpClients.createDefault()) {
            HttpGet httpget = new HttpGet("http://localhost:4000");
            ResponseHandler<String> responseHandler = response -> EntityUtils.toString(response.getEntity());
            return client.execute(httpget, responseHandler);
        } catch (IOException ignored) {
        }
        throw new RuntimeException("Request Failed!");
    }
}
```