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
es.awaitTermination(1, TimeUnit.MINUTES);
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