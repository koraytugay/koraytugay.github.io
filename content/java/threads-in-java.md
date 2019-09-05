---
layout: default
title:  "Threads in Java"
---

# Threads in Java
{:.no_toc}

* TOC
{:toc}

## Thread vs thread
`Thread` is a class in Java where as `thread` is an individual, lightweight _process_. Every thread has its own call stack, in other words: a call stack executes in a separate thread.

## Starting a Thread
A thread in Java starts as an instance of a `java.lang.Thread`. Every thread of execution gets started and run by an instance of Thread. A Thread executes the target `java.lang.Runnable` in a new thread.

```java
Runnable r = () -> {};
new Thread(r).start();
```

A single instance of a Runnable can be passed to and executed by multiple threads. That would mean the same job will be processed multiple times (albeit in different threads).

```java
Runnable r = () -> {};

new Thread(r).start();
new Thread(r).start();  // This is fine 
```

## The Thread Scheduler
The Thread Scheduler is the part of the JVM which pulls alive threads from the thread pool and moves them between runnable and running states. A Thread can influence / notify the scheduler on its intend using the following methods:

```java
static void sleep(long millis) throws InterruptedException
static void yield()
void join() throws InterruptedException
```

## Thread States
A Thread is considered to be:

- `new` until `start` is called.
- `alive` once `start` is called.
- `dead` once execution is finished.
  - A dead thread can never be re-started.

### Alive Threads
An alive thread can be in one of the following states:

- runnable
- running
- blocked / waiting / sleeping

#### Runnable State
A thread is considered to be in runnable state when it is started but not actually being executed. It may be moved to running state any time by the Thread Scheduler.

#### Running State
A thread is considered to be in running state while it is actually being executed. A thread in the running state can..
- finish executing and move to dead state.
- be moved back to runnable state any time by the Thread Scheduler.
- move to blocked / waiting / sleeping state by either an intention or lack of a required resource.

#### Blocked / Waiting / Sleeping State
A thread that has been started and was in `running` state at least once but not currently being actually executed will be in either of these states. Such a thread can only be moved to runnable state and never to running state immediatly. It may also stay in its current state in case the reason never vanishes that blocks or makes the thread sleep.

## Preventing Thread Execution
### Making a Thread Sleep
A thread goes to sleep when the static method `sleep` is called within its call stack.

```java
Thread.sleep(long millis) throws InterruptedException
```

Note that:
- A thread can never make any other thread sleep.
- `millis` passed to the method is the minimum durartion of sleep and it is not exact.
- After waking up, thread ends up in the runnable state. 
- A thread does not release the locks its holding when it goes to sleep.

### Making a Thread Yield
`static yield()` influences the Thread Scheduler to move the current running thread to runnable state. There is no guarantee that it will have any effect. Even if the thread moves to runnable state, it might be picked up immediately and moved to running state.

### Making a thread Join Another thread
`join() throws InterruptedException` moves the current thread to blocked state until the thread being joined finishes.

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
longRunningThread.join();  // Blocks until longRunningThread finishes
out.println("I can continue!");

// I will wait for my long running friend!
// I am done!
// I can continue!
```

## Thread Problems and Synchronisation
### Race Condition
A race condition can be summarised as a thread using (or _racing in to_) a resource while another thread is doing an operation on the very same resource that is supposed to be atomic.

__Example__
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

Account account = new Account();
Client alice = new Client(account);
Client bob = new Client(account);

Thread threadAlice = new Thread(alice);
Thread threadBob = new Thread(bob);

threadAlice.start();
threadBob.start();

threadAlice.join();  // join the main thread
threadBob.join();    // join the main thread

out.println(account.balance); // Most of the time 0, -10 from time to time
```

The race condition occurs as follows. Assuming account balance is currently 10:

1. alice checks `account.balance > 0`.
1. Before alice calls `account.withdraw()`, bob _races in_.
1. bob also checks `account.balance > 0` and at this point alice has not withdrawn yet.
1. alice calls `account.withdraw()` and balance becomes `0`.
1. bob calls `account.withdraw()` while balance actually is `0`.
1. Account balance becomes `-10`.

### Making Operations Atomic
#### Using Synchronisation
We must guarantee that checking the balance and withdrawing is atomic. We can make use of `synchronized` as seen in the below example.

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
- Every object has a single lock associated with it.
- When a thread enters a synchronised method of an object, the thread acquires the lock associated with the object.
  - A thread is not guaranteed to run until the end of the synchronised block, however even if the thread goes to runnable state, it does not release the lock, hence avoiding any other thread entering the synchronised block.
  - Not only the synchronised block itself, no other synchronised blocks can be entered by any other threads.
- Methods in a class can be synchronised on arbitrary locks, it does not have to be their own locks.
- A thread can hold more than a single lock.
- A thread is free to enter other methods that requires the lock it is holding.
- Only methods and blocks can be synchronized, classes and variables can not be synchronized.
- Synchronising static methods uses the lock of the class instance, which is the the instance of `Class` associated with the class where the static method is defined.
  - For a `class Foo`, that would be: `Class clazz = Foo.class`.

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
Synchronising a method is identical to having a synchronised block on `this` in the method.

```java
synchronized void foo() {}

void foo() {
    synchronized (this) {}
}
```

### Thread Safety
A class is said to be thread-safe if its data is protected by synchronized methods / blocks. However it still needs consideration to use thread-safe classes.

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

Thread a = new Thread(r);
Thread b = new Thread(r);

a.start();
b.start();
```

The example above will throw an `IndexOutOfBoundsException` even though we are using a `SynchronizedList`.
In a `SynchronizedList` each individual method is `synchronized`. There is nothing stopping from the following:
1. `a` asks if the list is empty, acquiring the lock of the list
  - At this point `b` can not access any methods of the list
1. `a` releases the lock knowing the list is not empty
1. `a` goes to runnable state
1. `b` asks the list if it is empty acquiring the lock
  - At this point `a` can not access any method of the list
1. `b` releases the lock knowing the list is not empty
1. `a` gets its turn and removes the item from the list
  - At this point `b` can not call the remove method
1. `a` releases the lock
1. `b` removes the element and the exception is thrown

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

Thread a = new Thread(r);
Thread b = new Thread(r);

a.start();
b.start();
```

### Deadlock
A deadlock happens when a thread acquires `lock_1` and starts waiting for `lock_2` to be released, where `lock_2` is acquired by another thread which is waiting for `lock_1` to be released. Neither thread is ever able to acquire the lock it is waiting for, and neither thread ever releases the lock it is holding.

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

DeadLockExample deadLockExample = new DeadLockExample();

Runnable foo = () -> deadLockExample.foo();
Runnable bar = () -> deadLockExample.bar();

new Thread(foo).start();
new Thread(bar).start();
```

### Thread Starvation
Thread starvation happens when a thread acquires a lock on a shared resource and goes onto a long running process or an infinite loop, never giving a chance to the other thread to proceed.

## wait - notify
A thread can acquire a lock on an object, and then may decide to release its lock by calling the `wait` method until `notify` is called on the same object by some other thread.

```java
void wait() throws InterruptedException
void notify()
void notifyAll()
```

For example:
1. `thread-a` acquires the lock on `list`
1. `thread-a` queries for the size of the `list`
1. `thread-a` sees that `list` is empty
1. Instead of releasing the lock and sleeping only to acquire the lock again to check if the list is empty, `thread-a` can call `wait` on the `list` object (which causes `thread-a` to release the lock its holding) which will block the execution of `thread-a` until `notify` is called on the same object
1. `thread-b` acquires the lock on `list`
1. `thread-b` inserts an item in the `list`
1. `thread-b` calls `notify` on the list, unlike `wait` this does not end up in releasing the lock immediately
1. `thread-b` releases the lock on `list` by exiting the `synchronized` block
1. `thread-a` gets notified and starts running, acquiring the lock on `list`

<table>
    <tr>
        <th style="width: 25%">wait()</th>
        <th style="width: 25%">wait(long timeout)</th>
        <th style="width: 25%">notify()</th>
        <th style="width: 25%">notifyAll()</th>
    </tr>
    <tr>
        <td colspan="4">Can only be called on objects where the lock is acquired.</td>
    </tr>
    <tr>
        <td colspan="4">Throws <code>IllegalMonitorStateException</code> which is an unchecked exception, hence need not to be handled.</td>
    </tr>
    <tr>
        <td colspan="2">Throws checked exception <code>InterruptedException</code>.</td>
        <td colspan="2">Does not throws any checked exceptions.</td>
    </tr>
    <tr>
        <td colspan="2">Releases the lock immediately and blocks the thread.</td>
        <td colspan="2">Does not release the lock immediately.</td>
    </tr>
    <tr>
        <td colspan="1">Waits until <code>notify</code> is called on the object.</td>
        <td colspan="1">Waits until <code>notify</code> is called on the object or timeout occurs.</td>
        <td>Only one of the threads waiting on the object is notified, which one is undefined.</td>
        <td>All of the threads waiting on the object is notified, which one will run first is undefined.</td>
    </tr>
</table>

### wait - notify Example

```java
class Notifier implements Runnable {
    Stack<String> messages;

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
                String mostRecentMsg = messages.pop();
                System.out.println(LocalTime.now() + " " + mostRecentMsg);
                if (mostRecentMsg.equals("q"))
                    System.exit(-1);
            }
        }
    }
}

class Receiver implements Runnable {
    Stack<String> messages;

    public Receiver(Stack<String> messages) {
        this.messages = messages;
    }

    @Override
    public void run() {
        while (true) {
            Scanner scanner = new Scanner(System.in);
            String msg = scanner.nextLine();
            synchronized (messages) {
                messages.push(msg);
                messages.notify();
            }
        }
    }
}

Stack<String> messages = new Stack<>();

new Thread(new Notifier(messages)).start();
new Thread(new Receiver(messages)).start();

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
