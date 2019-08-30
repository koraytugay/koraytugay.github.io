---
layout: default
title:  "Threads in Java"
---

# Threads in Java
{:.no_toc}

* TOC
{:toc}

## Thread vs thread
- `Thread` is a class in Java
- `thread` is an individual, lightweight process

Every thread has its own call stack, in other words: a call stack executes in a separate thread.

## Creating and Starting a Thread
- A thread in Java starts as an instance of a `java.lang.Thread`
  - Every thread of execution starts as an instance of class Thread

### Passing a Runnable to a Thread
A Thread executes the target `Runnable` in a new thread.

```java
Runnable r = () -> {};
new Thread(r).start();
```

A single instance of a Runnable can be passed to multiple Threads. That would mean the exact same job will be repeated multiple times (albeit in different threads).

```java
Runnable r = () -> {};

new Thread(r).start();
new Thread(r).start();  // This is fine 
```

#### Determining the Thread executing the Runnable

```java
Runnable r = () -> {
    out.println("Executed by: " + Thread.currentThread().getName());
};
new Thread(r, "foo").start();
new Thread(r, "bar").start();

// Executed by: foo
// Executed by: bar
```

## Thread States
A Thread is
- `new` until the `start` is called
- `alive` once the `start` is called
- `dead` once the `run` method (called from the `start`) finishes execution

### Alive Threads
An alive thread can be in the following states:
- runnable
- running
- blocked / waiting / sleeping

## The Thread Scheduler

The Thread Scheduler is the part of the JVM which pulls alive threads from the thread pool and moves them between runnable and running states. A Thread can influence / notify the scheduler on its intend using the following methods:

```java
static void sleep(long millis) throws InterruptedException
static void yield()
void join() throws InterruptedException
```

### Runnable State
- A thread has been started but is not actually running
- It may be moved to `running` state any time by the thread scheduler

### Running State
- A Thread that has been started and actually being executed
- It can finish executing and move to `dead` state
- It can be moved back to `runnable` state any time by the thread scheduler
- It can move to `blocked / waiting / sleeping` state by either an intention or lack of a required resource
  - Intentions can be `sleep`, `yield` or `join`

### Blocked / Waiting / Sleeping State
- A thread that has been started and was in `running` state at least once
- Moved to this state due to one of the reasones mentioned in `running` state
- Can only move back to `runnable` state (and never directly to `running` state) once the reason (or intention) vanishes / expires
  - It may never go back to `runnable` if the reason never vanishes / expires

## Preventing thread Execution
### Making a thread Sleep

```java
Thread.sleep(long millis) throws InterruptedException
```

- You can only make the current executing thread sleep, you can not make any other threads sleep
- `millis` you pass into the method is the minimum duration of sleep
  - It is not exact
- After waking up, the thread will end up in the runnable state
  - It will not go to running state directly
- A thread does not release the locks its holding when it goes to sleep

### Making a Thread Yield
- `static Thread.yield()` is to influence the thread scheduler to move the current running thread to `runnable state`
- There is no guarantee that it will have any effect
  - Even if the thread moves to `runnable` state, it might be picked up immediately and moved to `running` state

### Making a thread Join Another thread
- `join() throws InterruptedException` will move the current thread to `sleeping` state until the thread being joined finishes

```java
Runnable longRunning = () -> {
    try {
        Thread.sleep(4000);
        System.out.println("I am done!");
    } catch (InterruptedException ignored) {}
};

Thread longRunningThread = new Thread(longRunning);
longRunningThread.start();

out.println("I will wait for my long running friend!");
longRunningThread.join();
out.println("I can continue!");

// I will wait for my long running friend!
// I am done!
// I can continue!
```

## Thread Problems and Synchronisation
### Race Condition
- A thread using (or _racing in in to_) a resource while another thread is doing an operation that is supposed to be atomic

#### Race Condition Example
The following example in my case prints `0` for the most of the time, but not every time. The value seen in the console will be `-10` from time to time.

```java
class Account {
    int balance = 50;
    int withdraw() {
        balance = balance - 10;
        return 10;
    }
}

class Client implements Runnable {
    Account account;
    public Client(Account account) {
        this.account = account;
    }
    @Override
    public void run() {
        while (account.balance > 0) {
            try {
                Thread.sleep(15);
            } catch (InterruptedException ignored) {}
            account.withdraw();
        }
    }
}

final Account account = new Account();
final Client alice = new Client(account);
final Client bob = new Client(account);

final Thread threadAlice = new Thread(alice);
final Thread threadBob = new Thread(bob);

threadAlice.start();
threadBob.start();

threadAlice.join();
threadBob.join();

System.out.println(account.balance); // Most of the time 0, -10 from time to time
```

The race condition here is as follows, assuming when account balance is currently 10:

1. alice checks `account.balance > 0` and sees it is
1. Before alice calls `account.withdraw()`, bob races in
1. bob checks `account.balance > 0` and since alice did not do the withdrawal yet, bob also sees it is
1. alice calls `account.withdraw()` and balance becomes `0`
1. bob thinking balance is `10` calls `account.withdraw()` while balance actually is `0`
1. Account balance becomes `-10`

### Making Operations Atomic
#### Using Synchronisation
We must somehow guarantee that checking the balance and withdrawing is atomic. We can make use of `synchronized` as seen in the below example.

```java
class ThreadSafeAccount {
    int balance = 50;
    synchronized int withdraw() {
        if (balance == 0)
            return 0;
        
        balance = balance - 10;
        return 10;
    }
}
```

Once a thread enters the `withdraw` method, it is guaranteed that no other thread can enter any synchronized methods (including `withdraw`), making balance overdraw impossible.

#### Synchronisation and Locks
- Every object has a single lock associated with it
- When a thread enters a synchronised method of an object, the thread acquires the lock associated with the object
  - A thread is not guaranteed to run until the end of the synchronised method / block, however even if the thread goes to runnable state, it does not release the lock, hence avoiding any other thread entering the synchronised block/method
  - Not only the synchronised block itself, no other synchronised block can be entered by any other thread
- Methods in a class can be synchronised on arbitrary locks, it does not have to be their own locks
- A thread can hold more than one lock
- A thread is free to call (and enter) other methods that requires the lock it is holding
- Only methods and blocks can be synchronized, classes and variables can not be synchronized
- Synchronising static methods uses the lock of the class instance
  - That is an instance of the class `Class` associated with the class where the static method is defined
  - For a `class Foo`, that would be: `Class clazz = Foo.class;`

#### Arbitrary Lock Example

```java
class Foo {
    Object lock_1 = new Object();
    Object lock_2 = new Object();

    void foo() {
        synchronized (lock_1) {}
    }

    void bar() {
        synchronized (lock_2) {}
    }
}
```

In the example above, assume an instance of `Foo` is shared between 2 threads, namely `a` and `b`. Assume `a` enters `foo`, which would make `a` acquire `lock_1`. At this point, if `b` tries to enter `foo`, it would not succeed. However `b` is free to enter `bar` by acquiring the lock of `lock_2` which is free.

#### Synchronized Method vs Synchronized Block
- Synchronising a method is identical to having a synchronised block on `this` in the method

```java
synchronized void foo() {
    // implementation
}

void foo() {
    synchronized (this) {
        // implementation
    }
}
```

### Thread Safety
A class is said to be thread-safe if its data is protected by synchronized methods / blocks.
However it still needs consideration to use thread-safe classes.

```java
List<String> synchronizedList = 
    Collections.synchronizedList(new ArrayList<>());
synchronizedList.addAll(Arrays.asList("foo"));

Runnable r = () -> {
    if (synchronizedList.size() > 0) {
        try {
            Thread.sleep(10);
            String remove = synchronizedList.remove(0);
            if (remove == null) {
                System.exit(-1);
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
};

new Thread(r).start();
new Thread(r).start();
```

The example above will throw an `IndexOutOfBoundsException` even though we are using a `SynchronizedList`.
In a `SynchronizedList` each individual method is `synchronized`. There is nothing stopping from the following:
- `thread a` asks if the list is empty, acquiring the lock of the list
  - At this point `thread b` can not access any methods of the list
- `thread a` releases the lock knowing the list is not empty
- `thread a` goes to runnable state
- `thread b` asks the list if it is empty acquiring the lock
  - At this point `thread a` can not access any method of the list
- `thread b` releases the lock knowing the list is not empty
- `thread a` gets its turn and removes the item from the list
  - At this point `thread b` can not call the remove method
- `thread a` releases the lock
- `thread b` removes the element and the exception is thrown

It is better to use just a plain old ArrayList, but synchronising on the list itself as seen below.

```java
List<String> synchronizedList = new ArrayList<>();
synchronizedList.addAll(Arrays.asList("foo"));

Runnable r = () -> {
    synchronized (synchronizedList) {
        if (synchronizedList.size() > 0) {
            try {
                Thread.sleep(10);
                String remove = synchronizedList.remove(0);
                if (remove == null) {
                    System.exit(-1);
                }
            } catch (InterruptedException e) {}
        }
    }
};

new Thread(r).start();
new Thread(r).start();
```

### Deadlock
A deadlock happens when a thread acquires `lock_1` and starts waiting for `lock_2` to be released, where `lock_2` is acquired by another thread which is waiting for `lock_1` to be released.
Neither thread is ever able to acquire the lock it is waiting for, and neither thread ever releases the lock it is holding.

```java
class DeadLockExample {

    Object lock_1 = new Object();
    Object lock_2 = new Object();

    void foo() {
        synchronized (lock_1) {
            try {Thread.sleep(10);}
            catch (InterruptedException e) {}
            synchronized (lock_2) {
                System.out.println("foo");
            }
        }
    }

    void bar() {
        synchronized (lock_2) {
            try {Thread.sleep(10);}
            catch (InterruptedException e) {}
            synchronized (lock_1) {
                System.out.println("foo");
            }
        }
    }
}

final DeadLockExample deadLockExample = new DeadLockExample();

Runnable foo = () -> deadLockExample.foo();
Runnable bar = () -> deadLockExample.bar();

new Thread(foo).start();
new Thread(bar).start();
```

### Thread Starvation
Thread starvation happens when a thread acquires a lock on a shared resource and goes onto a long running process or an infinite loop, never giving a chance to the other thread to proceed.

## wait - notify
A thread can acquire a lock on an object, and then may decide to release its lock by calling the `wait` method on the object, until `notify` is called on the same object by some other thread.

```java
void wait() throws InterruptedException
void notify()
void notifyAll()
```

For example:
- `thread-a` acquires the lock on `list`
- `thread-a` queries for the size of the `list`
- `thread-a` sees that `list` is empty
- Instead of releasing the lock, sleeping for a while, acquiring the lock again and checking if the list is empty, `thread-a` can call `wait` on the `list` object (which causes `thread-a` to release the lock its holding) which will block the execution of `thread-a` until `notify` is called on the same object
- `thread-b` acquires the lock on `list`
- `thread-b` inserts an item in the `list`
- `thread-b` calls `notify` on the list
  - unlike `wait` this does not end up in releasing the lock immediately
- `thread-b` releases the lock on `list` its holding by exiting the `synchronized` method/block
- `thread-a` gets notified and starts running again acquiring the lock on `list`

<table>
    <tr>
        <th style="width: 25%">wait()</th>
        <th style="width: 25%">wait(long timeout)</th>
        <th style="width: 25%">notify()</th>
        <th style="width: 25%">notifyAll()</th>
    </tr>
    <tr>
        <td colspan="4">Can only be called on objects where the lock is acquired</td>
    </tr>
    <tr>
        <td colspan="4">Throws <code>IllegalMonitorStateException</code> which is an unchecked exception, hence need not to be handled</td>
    </tr>
    <tr>
        <td colspan="2">Throws checked exception <code>InterruptedException</code></td>
        <td colspan="2">Does not throws any checked exceptions</td>
    </tr>
    <tr>
        <td colspan="2">Releases the lock immediately and blocks the thread</td>
        <td colspan="2">Does not release the lock immediately</td>
    </tr>
    <tr>
        <td colspan="1">Waits until <code>notify</code> is called on the object</td>
        <td colspan="1">Waits until <code>notify</code> is called on the object or timeout occurs</td>
        <td>Only one of the threads waiting on the object is notified, which one is undefined</td>
        <td>All of the threads waiting on the object is notified, which one will run first is undefined</td>
    </tr>
</table>

### wait - notify Example

```java
class Notifier implements Runnable {
    final Stack<String> messages;

    public Notifier(Stack<String> messages) {
        this.messages = messages;
    }

    @Override
    public void run() {
        while (true) {
            synchronized (messages) {
                if (messages.empty()) {
                    try {
                        messages.wait();
                    } catch (InterruptedException ignored) {}
                }
                final String mostRecentMsg = messages.pop();
                System.out.println(LocalTime.now() + " " + mostRecentMsg);
                if (mostRecentMsg.equals("q"))
                    System.exit(-1);
            } // synchronized
        } // while
    } // run
} // Notifier

class Receiver implements Runnable {
    final Stack<String> messages;

    public Receiver(Stack<String> messages) {
        this.messages = messages;
    }

    @Override
    public void run() {
        while (true) {
            final Scanner scanner = new Scanner(System.in);
            final String msg = scanner.nextLine();
            synchronized (messages) {
                messages.push(msg);
                messages.notify();
            }
        }
    }
}

final Stack<String> messages = new Stack<>();

new Thread(new Notifier(messages)).start();
new Thread(new Receiver(messages)).start();

// Sample execution
// Hello World!
// 12:20:50.057 Hello World!
// Thank you!
// 12:20:52.113 Thank you!
// q
// 12:20:52.989 q
//
// Process finished with exit code 255

```
### wait - notify Example - 2

```java
Object lock = new Object();

new Thread(() -> {
    synchronized(lock) {
        try {
            lock.wait();
        } catch (InterruptedException e) {}
    }
    System.out.println("Notified!");
}).start();

try {
    Thread.sleep(500);
} catch (InterruptedException e) {}

System.out.print("Press any button to continue.");
new Scanner(System.in).nextLine();
synchronized (lock) {
    lock.notifyAll();
}

// Press any button to continue.
// (Users hits enter in console)
// Notified!
```
