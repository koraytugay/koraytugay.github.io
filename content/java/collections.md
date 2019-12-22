---
layout: default
title:  "Collections"
---

# Collections
{:.no_toc}

* TOC
{:toc}

## Arrays
### Creating an Empty Array
```java
int[] foo = new int[0];
int []bar = new int[0];
int baz[] = new int[0];
```

### Creating an Array with Initial Values
```java
int[] foo = {};
int[] bar = {1};
int[] baz = new int[]{1};
```

### Mixing Arrays In to Multiple Variable Declarations
```java
int[] foo, bar; // both arrays
int []foo, bar; // both arrays
int foo[], bar; // foo is an array bar is an int
```

### Passing an Initalised Array to a Method
```java
void arr(int[] arr) {};

arr(new int[]{}); // This is fine

// This will not compile
// arr({});
```

### Arrays to Lists
- `Arrays.asList` returns a fixed-size List
  - Trying to add or remove elements throws a RuntimeException
```java
String[] arr = {"foo", "bar"};
List<String> list = Arrays.asList(arr);
list.add("baz");  // throws UnsupportedOperationException
```

## Queue
- Found in [java.util.Queue](https://docs.oracle.com/javase/8/docs/api/java/util/Queue.html)
- Usually FIFO but not necessarily
- Items added to `tail`, removed from `head`
- Usually does not accept `null` values, must be avoided
- `add` throws Exception if element can not be added, `offer` returns false
- `remove` throws Exception if no element exists, `poll` returns false
- `element` throws Exception if no element exists, `peek` returns null

### First In First Out Queue Example
```java
Queue<Integer> iq = new LinkedList<>();
boolean itemadded;
itemadded = iq.offer(1);        // true
itemadded = iq.offer(2);        // true

System.out.println(iq.poll());  // 1
System.out.println(iq.poll());  // 2
System.out.println(iq.poll());  // null
```

### First In Last Out Queue Example
```java
Queue<Integer> iq = Collections.asLifoQueue(new LinkedList<Integer>());
boolean itemadded;
itemadded = iq.offer(1);        // true
itemadded = iq.offer(2);        // true

System.out.println(iq.poll());  // 2
System.out.println(iq.poll());  // 1
System.out.println(iq.poll());  // null
```

#### Interactive FIFO / LIFO Queue Example
```java
// If you want a FIFO Queue use this:
Queue<Integer> queue = new LinkedList<>();

// If you want a LIFO Queue use this:
// Queue<Integer> queue = Collections.asLifoQueue(new LinkedList<Integer>());

Scanner scanner = new Scanner(System.in);
while (true) {
    System.out.println("Command: (add, element, remove, print, q)");
    String command = scanner.nextLine();
    switch (command) {
        case "add":
        queue.add(Integer.valueOf(scanner.nextLine()));
        break;
        
        case "element":
        System.out.println(queue.element());
        break;
        
        case "remove":
        System.out.println("Removed: " + queue.remove());
        break;

        case "print":
        System.out.println(queue.stream().map(val -> String.valueOf(val)).collect(Collectors.joining(" ")));
        break;
        
        case "q":
        System.exit(-1);
    }
}
```
Sample run with First In First Out Behavior
```text
Command: (add, element, remove, print, q)
add
100
Command: (add, element, remove, print, q)
add 
150
Command: (add, element, remove, print, q)
element
100
Command: (add, element, remove, print, q)
remove
Removed: 100
Command: (add, element, remove, print, q)
remove
Removed: 150
Command: (add, element, remove, print, q)
q
```

Sample run with First In Last Out Behavior
```text
Command: (add, element, remove, print, q)
add
100
Command: (add, element, remove, print, q)
add
150
Command: (add, element, remove, print, q)
element
150
Command: (add, element, remove, print, q)
remove
Removed: 150
Command: (add, element, remove, print, q)
remove
Removed: 100
Command: (add, element, remove, print, q)
q
```

## Collections Features Related to Java 8
[Iterable.forEach(Consumer)](https://docs.oracle.com/javase/8/docs/api/java/lang/Iterable.html#forEach-java.util.function.Consumer-)
```java
Consumer<Integer> integerConsumer = i -> {}; // no-op consumer
Arrays.asList(1, 2).forEach(integerConsumer);
```
__Heads Up!__ `IntConsumer` does not extend `Consumer<Integer>`
```java
IntConsumer intConsumer = i -> {};
// This will not compile:
// Arrays.asList(1, 2).forEach(intConsumer);
```
[Collection.removeIf(Predicate)](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html#removeIf-java.util.function.Predicate-)
```java
List<String> list = new ArrayList<>();
list.add("foo");
list.add("bar");
list.removeIf(s -> s.startsWith("f")); // foo is removed. list only contains [bar]
```
[List.replaceAll(UnaryOperator)](https://docs.oracle.com/javase/8/docs/api/java/util/List.html#replaceAll-java.util.function.UnaryOperator-)
```java
List<String> list = new ArrayList<>();
list.add("foo");
list.add("bar");
list.replaceAll(s -> s.toUpperCase()); // [FOO, BAR]
```
[List.sort(Comparator)](https://docs.oracle.com/javase/8/docs/api/java/util/List.html#sort-java.util.Comparator-)
```java
List<String> list = new ArrayList<>();
list.add("foobarbaz");
list.add("foobar");
list.add("foo");
list.sort((o1, o2) -> o1.length() - o2.length()); // [foo, foobar, foobarbaz]
```

[Map.computeIfPresent(BiFunction)](https://docs.oracle.com/javase/8/docs/api/java/util/Map.html#computeIfPresent-K-java.util.function.BiFunction-)
```java
static Map<String, Integer> nameAge = new HashMap<>();
static {
    nameAge.put("koray", 34);
    nameAge.put("deniz", 1);
}
nameAge.computeIfPresent("koray", (name, age) -> age + 10); // assigns a new value to the key
nameAge.computeIfPresent("deniz", (name, age) -> null);     // returning null removes the key from the map
System.out.println(nameAge);                                // {koray=44}
```

[Map.putIfAbsent](https://docs.oracle.com/javase/8/docs/api/java/util/Map.html#putIfAbsent-K-V-) and [Map.computeIfAbsent](https://docs.oracle.com/javase/8/docs/api/java/util/Map.html#computeIfAbsent-K-java.util.function.Function-)
```java
Map<String, Integer> nameAge = new HashMap<>();

// putIfAbsent returns the existing value for the key
nameAge.putIfAbsent("kt", 34); // value inserted, null returned
nameAge.putIfAbsent("kt", 35); // value not inserted, 34 returned

nameAge.clear();

// computeIfAbsent returns the inserted value
nameAge.computeIfAbsent("kt", key -> 34); // value inserted, 34 returned
nameAge.computeIfAbsent("kt", key -> 35); // value not inserted, 34 returned
```
- Further reading: [here](https://stackoverflow.com/a/48184207)

## Concurrent Collections
### BlockingQueue
- Found in [java.util.concurrent.BlockingQueue](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/BlockingQueue.html)
#### First In First Out Blocking Queue Example
```java
BlockingQueue<String> bq = new LinkedBlockingQueue<>(2); // Initial capacity

Runnable letterCounter = () -> {
    try {
        Thread.sleep(10000);  // Letter Counter requires 10 seconds to warm up!
    } catch (InterruptedException e) {};
    while (true) {
        try {
            String toCount = bq.take();
            System.out.println("Finished counting letters in: " + toCount);
            System.out.println("Number of letters: " + toCount.length());
            Thread.sleep(10);  // counter needs to rest
        } catch (InterruptedException e) {}
    }
};

Runnable userInputReader = () -> {
    Scanner scanner = new Scanner(System.in);
    while (true) {
        System.out.println("Enter a sentence please:");
        String input = scanner.nextLine();
        if (!bq.offer(input)) {
            System.out.println("Blocked! We will add the sentence once queue is available!");
            try {
                bq.put(input);
                Thread.sleep(2000);  // Inform the user a little later
            } catch (InterruptedException e) {}
        }
        System.out.println("Your input: " + input + " is in queue!");
    }
};

new Thread(letterCounter).start();
new Thread(userInputReader).start();
```
Sample Output
```bash
Enter a sentence please:
koray
Your input: koray is in queue!
Enter a sentence please:
tugaaaaay
Your input: tugaaaaay is in queue!
Enter a sentence please:
lay lay lay lom
We are blocked! Please hold on! We will add the sentence once queue is available!
Finished counting letters in: koray
Number of letters: 5
Finished counting letters in: tugaaaaay
Number of letters: 9
Finished counting letters in: lay lay lay lom
Number of letters: 15
Your input: lay lay lay lom is in queue!
Enter a sentence please:
```
Notes
- Remember `put` and `take` blocks
- You can `offer` to see if value got accepted or not
  - If not you then block by calling `put`
  
### ConcurrentHashMap
- Found in [java.util.concurrent](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/package-summary.html)
- Offers atomic versions of `put`, `remove` and `replace` methods

#### ConcurrentHashMap Example
- The following method 
  - throws `ConcurrentModificationException` when called with `new HashMap<Integer, Integer>();`
  - returns `0` as expected when called with `new ConcurrentHashMap<Integer, Integer>();`
```java
int emptyMapConcurrent(Map<Integer, Integer> integerMap) {
    // Add some data in the map first
    IntStream.rangeClosed(1, 100).boxed().forEach(i -> integerMap.put(i, i));
    
    // Runnable to remove all pairs from the map
    Runnable r = () -> integerMap.keySet().forEach(integerMap::remove);

    Thread i = new Thread(r);
    Thread j = new Thread(r);

    i.start();
    j.start();

    try {
        i.join();
        j.join();
    } catch (InterruptedException e) {}

    return integerMap.size();
}
```