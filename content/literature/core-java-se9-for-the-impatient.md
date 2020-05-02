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
Runnables and Callables represent tasks that will run in separate threads. It does not make sense to have a 1:1 relationship between a task and a thread, in other words it does not make sense to spawn a new Thread for every task just to terminate it later. 

In the Java concurrency library, an `ExecutorService` schedules and executes tasks and allocates threads to them. 

### Runnables
A `Runnable` can either be `execute`d or `submit`ted. 

```java
ExecutorService executorService = Executors.newFixedThreadPool(2);

executorService.execute(myRunnable);
executorService.execute(anotherRunnable);
```

These calls will not block the execution. There are several options to wait for runnables to complete, very simple one being calling `shutdown` followed by `awaitTermination`:

```java
executorService.shutdown();
executorService.awaitTermination(1, TimeUnit.MINUTES);
```

### Callables and Futures
`Callable`s are `Runnable`s on steroids that return `Future`s which wraps the returning value of the task. `Callable`s are `submit`ted:

```java
Future<MyCallableReturnType> future = executorService.submit(myCallable);
MyCallableReturnType val = future.get();  // blocks current thread
```
