---
layout: default
title:  "Core Java SE9 for the Impatient"
---

# Core Java SE9 for the Impatient
{:.no_toc}

* TOC
{:toc}

## Chapter 10 - Concurrent Programming

### ExecutorService
`Runnable`s and `Callable`s represent tasks that will run in separate threads. It does not make sense to have a 1:1 relationship between a task and a thread, in other words it does not make sense to spawn a new Thread for every task just to terminate it later. 

In the Java concurrency library, an `ExecutorService` schedules and executes tasks and allocates threads to them. 

### Runnables
A `Runnable` can either be `execute`d or `submit`ted. 

```java
ExecutorService executorService = Executors.newFixedThreadPool(2);

executorService.execute(myRunnable);
executorService.execute(anotherRunnable);
```

These calls will not block the execution. There are several options to wait for `Runnable`s to complete, a simple one being calling `shutdown` followed by `awaitTermination`:

```java
executorService.shutdown();
executorService.awaitTermination(1, TimeUnit.MINUTES);
```

### Callables
`Callable`s are `Runnable`s on steroids that return `Future`s which wraps the returning value of the task. `Callable`s are `submit`ted:

```java
Future<MyCallableReturnType> future = executorService.submit(myCallable);
MyCallableReturnType val = future.get();  // blocks current thread
```

It is also possible to gracefully wait on a `Future` by making use of the `isDone` method:

```java
ExecutorService es = Executors.newFixedThreadPool(2);

Callable<Integer> callable = () -> {
    // Long running task..
    Thread.sleep(4000);
    return 42;
};

Future<Integer> future = es.submit(callable);

System.out.print("Processing..");
while (!future.isDone()) {
    Thread.sleep(1000);
    System.out.print(".");  // Feedback to user application did not freeze
}

System.out.println(future.get());  // Print the result

es.shutdown();
```

### CompletableFutures
`CompletableFuture`s are `Future`s on steroids that accept callbacks instead of returning a wrapped result. 

```java
ExecutorService es = Executors.newFixedThreadPool(2);

CompletableFuture.supplyAsync(() -> {
    try { Thread.sleep(2000); } catch (InterruptedException ignored) {}
    return 42;
}, es).whenComplete((integer, throwable) -> System.out.println(integer));
```

### Thread Safety
#### Visibility
What happens when you run the program below? How do you make it actually complete instead of running forever?

```java
static boolean done = false;

public static void main(String[] args) throws Exception {
    ExecutorService es = Executors.newCachedThreadPool();

    es.execute(() -> {
        for (int i = 0; i < 100; i++) {
            sleepOneMilliSecond();
        }
        System.out.println("Finished.");
        done = true;
    });

    es.execute(() -> {
        while (!done) {}
        System.out.println("I can continue.");
    });

    es.shutdown();
}

static void sleepOneMilliSecond() {
    try { Thread.sleep(1); } 
    catch (InterruptedException ignored) {}
}
```

#### Race Conditions
Race conditions happen whenever a value is shared between threads. Here is an example:

```java
static volatile int count = 0;

public static void main(String[] args) throws Exception {
    ExecutorService es = Executors.newCachedThreadPool();

    es.execute(() -> {
        for (int i = 0; i < 1000; i++) {
            sleepOneMilliSecond();
            count++;
        }
    });

    es.execute(() -> {
        for (int i = 0; i < 1000; i++) {
            sleepOneMilliSecond();
            count++;
        }
    });

    es.shutdown();
    es.awaitTermination(1, TimeUnit.MINUTES);  // wait for tasks to complete

    System.out.println(count);
}

static void sleepOneMilliSecond() {
    try { Thread.sleep(1); } 
    catch (InterruptedException ignored) {}
}
```

Will the printed value be `2000`?

### Locks and Conditions

#### notifyAll Example
Example below makes use of the `notifyAll` method. 2 threads, `a` and `b` both acquire the `lock`, and start `wait`ing on them. Main thread acquires the same `lock`, `sleep`s for 2 seconds and then calls `notifyAll`. We can observe both threads waiting on the `lock`s proceeding after main thread calls `notifyAll`.

```java
Object lock = new Object();
CountDownLatch countDownLatch = new CountDownLatch(2);

Thread a = new Thread(() -> {
    synchronized (lock) {
        try {
            countDownLatch.countDown();
            System.out.println("First thread is sleeping.");
            lock.wait();
            System.out.println("First thread continue.");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
});

Thread b = new Thread(() -> {
    synchronized (lock) {
        try {
            countDownLatch.countDown();
            System.out.println("Second thread is sleeping.");
            lock.wait();
            System.out.println("Second thread continue.");
            lock.notifyAll();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
});

a.start();
b.start();

// Do not continue unless threads a and b start
countDownLatch.await();

synchronized (lock) {
    System.out.println("Main thread is sleeping.");
    Thread.sleep(2000);
    System.out.println("Notifying all.");
    lock.notifyAll();
}

// Wait for both threads to complete
a.join();
b.join();
System.out.println("Goodbye!");
```