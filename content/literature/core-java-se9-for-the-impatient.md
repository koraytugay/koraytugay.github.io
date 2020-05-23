---
layout: default
title:  "Core Java SE9 for the Impatient"
---

# Core Java SE9 for the Impatient
{:.no_toc}

* TOC
{:toc}

## Chapter 5 - Exception Handling
### Try-With-Resources, Caused By and Suppressed Exceptions
What happens if an exception is thrown inside a `try` block, and afterwards again an exception is thrown when `close` is called? __Try-with-Resources__ is smart enough to add the exception thrown within `close` as a __suppressed exception__ to the exception thrown within the `try`. Here is an example:

```java
public class MyAutoClosable implements AutoCloseable {

    public void foo() {
        try {
            bar();
        } catch (NumberFormatException e) {
            throw new UnsupportedOperationException("foo failed!", e);
        }
    }

    void bar() {
        throw new NumberFormatException("bar failed!");
    }

    @Override
    public void close() {
        throw new IllegalStateException("close failed!");
    }
}
```

And executing the code above:

```java
try (MyAutoClosable myAutoClosable = new MyAutoClosable()) {
    myAutoClosable.foo();
}
```

Output will be:

```
Exception in thread "main" java.lang.UnsupportedOperationException: foo failed!
    at biz.tugay.MyAutoClosable.foo(MyAutoClosable.java:9)
    at MyMainClass.main(MyMainClass.java:6)
    Suppressed: java.lang.IllegalStateException: close failed!
        at biz.tugay.MyAutoClosable.close(MyAutoClosable.java:19)
        at MyMainClass.main(MyMainClass.java:7)
Caused by: java.lang.NumberFormatException: bar failed!
    at biz.tugay.MyAutoClosable.bar(MyAutoClosable.java:14)
    at biz.tugay.MyAutoClosable.foo(MyAutoClosable.java:7)
    ... 1 more
```

Exception thrown within `close` call is _suppressed_. Exception passed into the `UnsupportedOperationException` as cause is printed. Not all Exception classes have a constructor that takes a parameter to store the cause. In that case you need to call the `initCause` method.

To learn more about suppressed exceptions, read [this](https://stackoverflow.com/a/7849524) answer.

### Finally Block
> Sometimes, you need to cleanup a resource that is not `AutoClosable`. In that case use the `finally` block as follows:

```java
try {
    // do work...
} finally {
    // clean up
}
```

Things to keep in mind when using the __try - finally__ pattern:

- Do not throw exceptions in the `finally` block. If the body of the `try` block was terminated due to an exception, it will be masked by the exception thrown in `finally`. The suppression mechanism only works for try-with-resources statements. 
- Do not have `return` statements in `finally`. If the body of the `try` block has a `return` statement, it will be overridden.

Lets change the example above slightly:

```java
public class MyAutoClosable implements AutoCloseable {

    public void foo() {
        try {
            bar();
        } catch (NumberFormatException e) {
            throw new UnsupportedOperationException("foo failed!", e);
        }
    }

    private void bar() {
        throw new NumberFormatException("bar failed!");
    }

    @Override
    public void close() {
        throw new IllegalStateException("close failed!");
    }
}
```

and instead of using _try-with-resources_, lets use _try - finally_ approach:

```java
MyAutoClosable myAutoClosable = new MyAutoClosable();
try {
    myAutoClosable.foo();
} finally {
    myAutoClosable.close();
}
``` 

And the output will be:

```
Exception in thread "main" java.lang.IllegalStateException: close failed!
    at biz.tugay.MyAutoClosable.close(MyAutoClosable.java:19)
    at MyMainClass.main(MyMainClass.java:9)
```

__Danger!__ This is not most likely what we want.. We are only made aware of that `close` has failed. The exception thrown in `foo` is lost, gone to nirvana. I think this is one way to solve the problem:

```java
MyAutoClosable myAutoClosable = new MyAutoClosable();
Exception exception = null;
try {
    myAutoClosable.foo();
} catch (Exception e) {
    exception = e;
} finally {
    try {
        myAutoClosable.close();
    } catch (Exception e) {
        if (exception != null) {
            exception.addSuppressed(e);
        } else {
            exception = e;
        }
    } finally {
        if (exception != null) {
            throw exception;
        }
    }
}
```

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

### Threads
#### ThreadLocal
`ThreadLocal<T>` helper class can be used to keep data scope to a single thread only. Here is an example:

```java
public class UserContext {
    private static ThreadLocal<String> currentUserId = new ThreadLocal<>();

    public static String getCurrentUserId() {
        return currentUserId.get();
    }

    public static void setCurrentUserId(String currentUserId) {
        UserContext.currentUserId.set(currentUserId);
    }
}
```

`currentUserId` can safely be used among different threads by:
```java
UserContext.setCurrentUserId(userId);
UserContext.getCurrentUserId();
```

Another example can be presented using `NumberFormat` class, which is not thread safe. `NumberFormat` instances can be shared between threads as follows then:

```java
static ThreadLocal<NumberFormat> currencyFormat
    = ThreadLocal.withInitial(NumberFormat::getCurrencyInstance);
```

Every thread can call the following safely:

```java
currencyFormat.get().format(aNumber);
```

The first time you call `get` in a given thread, the lambda expression in the constructor is called to create the instance for the thread. From then on, the `get` method returns the instance belonging to the current thread.

### Chapter Notes
- A task that wants to be interruptible must periodically check for interruption requests. This is required for any tasks that you would like to cancel when some other task has succeeded.

### Exercises
> How large does an array have to be for `Arrays.parallelSort` to be faster than `Arrays.sort` on your computer?

Hard to tell.. 

```
Sorting 131072 integers..
Array Sort:8
Parallel sort:2

Sorting 65536 integers..
Array Sort:4
Parallel sort:1

Sorting 32768 integers..
Array Sort:2
Parallel sort:1

Sorting 16384 integers..
Array Sort:1
Parallel sort:0

Sorting 8192 integers..
Array Sort:1
Parallel sort:0

Sorting 4096 integers..
Array Sort:0
Parallel sort:1
Parallel sort started losing!
```

When `parallelSort` starts losing, the number of items sorted are so little, benchmark starts making no sense. `parallelSort` starts becoming significantly faster even around 250k items:

```
Sorting 262144 integers..
Array Sort:22
Parallel sort:3
```

> Write an application in which multiple threads read all words from a collection of files. Use a `ConcurrentHashMap<String, Set<File>>` to track in which files each word occurs. Use the `merge` method to update the map.

In order to solve this problem I created 10 _lorem ipsum_ files and dumped them in `/Users/kt/lorem`. Here is my solution:

```java
Map<String, Set<File>> wordFileMap = new ConcurrentHashMap<>();
List<Path> loremFiles = 
    Files.list(Paths.get("/Users/kt/lorem")).collect(toList());

ExecutorService es = Executors.newFixedThreadPool(10);
loremFiles.forEach(path -> es.submit(() -> {
    try {
        File file = path.toFile();
        Scanner scanner = new Scanner(file, StandardCharsets.UTF_8.name());
        scanner.useDelimiter("\\W+");
        while (scanner.hasNext()) {
            String word = scanner.next();
            word = word.trim();
            wordFileMap.merge(word,
                    new HashSet<>(singletonList(file)),
                    (existing, found) -> {
                existing.addAll(found);
                return existing;
            });
        }
        scanner.close();
    } catch (IOException e) {
        throw new UncheckedIOException(e);
    }
}));
es.shutdown();
es.awaitTermination(1, TimeUnit.MINUTES);

// Checksum to be used when trying for different approaches
// Calculate checksum by sorted keys and sorted values
StringBuilder sb = new StringBuilder();
wordFileMap.keySet().stream().sorted().forEach(s -> {
    String files = wordFileMap.get(s).stream()
            .map(File::toString)
            .sorted().collect(Collectors.joining());
    sb.append(s);
    sb.append(files);
});

MessageDigest digest = MessageDigest.getInstance("MD5");
byte[] md5 = digest.digest(sb.toString().getBytes(StandardCharsets.UTF_8));
System.out.println(DatatypeConverter.printHexBinary(md5).toLowerCase());
```

This approach seems to be thread safe, making use of the `merge` method. I also tried making use of `putIfAbsent` but turned out to be not thread safe. See [this](https://stackoverflow.com/questions/61718067) discussion for details.
