---
layout: default
title:  "Streams"
---

# Streams
{:.no_toc}

* TOC
{:toc}

## Overview
- Found in [java.util.stream.Stream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html)
- Do not hold values
- Can be infinite

Consists of..
- A Source
- Zero to Many Intermediate Operations
  - Methods for transformations, methods that accept behavioral parameters which return streams
- A Terminal Operation

Compared to collections..
> A collection is a set of values spread out in space, a stream is a set of values spread out in time.
>
> [Modern Java in Action](https://www.manning.com/books/modern-java-in-action)

### Cheat Sheet

```java 
// Creating Streams
Collection.stream()
Stream.of(T...)
Stream.generate(Supplier)
Stream.iterate(T, UnaryOperator)
Arrays.stream(T[])

// Transformations
Stream.filter(Predicate)
Stream.map(Function)
Stream.flatMap(Function)
Stream.skip(long)
Stream.limit(long)
Stream.distinct()
Stream.sorted(Comparator)
Stream.mapToInt(ToIntFunction)
Stream.flatMapToInt(Function)

// Terminations
Stream.count()
Stream.min(Comparator)
Stream.max(Comparator)
Stream.findAny()
Stream.anyMatch(Predicate)
Stream.toArray(IntFunction)
Stream.collect(Collector)
```

## Creating Streams

### Creating a Stream from a Collection
```java
Collection<Integer> ints = Arrays.asList(new Integer(1), new Integer(2));
ints.stream(); // Stream<Integer>
```
### Creating Streams Using Stream Methods

```java
// passing an array of Integer objects
Stream.of(new Integer[]{new Integer(1), new Integer(2)}); // Stream<Integer>

// harnessing varargs
Stream.of(new Integer(1), new Integer(2)); // Stream<Integer>

// Generating an infinite Stream<Integer>, 
// initialised with random values between 1 and 10
Stream.generate(() -> new Integer(ThreadLocalRandom.current().nextInt(1, 11)));

// An infinite Stream of Integers, initialised with incremented values
Stream.iterate(new Integer(1), i -> new Integer(i.val + 1)); // Stream<Integer>
```

### Creating Streams Using Arrays Methods

```java
Arrays.stream(new Integer[]{new Integer(1), new Integer(2)}); // Stream<Integer>
// Arrays.stream(new Integer(1), new Integer(2)) will not work!
```

#### Arrays.stream vs Stream.of

```java
import java.util.stream.IntStream;
import java.util.stream.Stream;

import static java.util.Arrays.*;
import static java.util.stream.Stream.*;

class Foo {    
    void foo() {
        Stream<Foo> foo; 

        foo =     of(new Foo(), new Foo());
     // foo = stream(new Foo(), new Foo()); not possible

        foo =     of(new Foo[]{new Foo(), new Foo()});
        foo = stream(new Foo[]{new Foo(), new Foo()});

        Stream<Integer> integerStream; 

        integerStream =     of(1, 2);
     // integerStream = stream(1, 2); not possible

        integerStream =     of(new Integer[]{1, 2});
        integerStream = stream(new Integer[]{1, 2});

        Stream<int[]> intArrayStream =     of(new int[]{1, 2}); // count = 1!
        IntStream intStream          = stream(new int[]{1, 2}); // count = 2!
    }
}
```

## Intermediate Operations on Streams

### Filtering
```java
Stream.of(1, 2).filter(i -> i < 2); // Only Integer[1] remains
```

### Mapping
- Transforms each element of the `Stream` using the provided `Function`
- Returns a new `Stream` with the return type of the provided `Function`

```java
Stream.of(1, 2).map(i -> String.valueOf(i)); // Stream<String>
```

### FlatMapping

```java
import java.util.stream.Stream;

class Foo {    
    String foo, bar;
    
    Foo(String foo, String bar) {this.foo = foo; this.bar = bar;}
    
    void flatMap() {
        // returns Stream<String> : ["foo", "bar", "baz", "qux"]
        Stream.of(new Foo("foo", "bar"), new Foo("baz", "qux"))
              .flatMap(f -> Stream.of(f.foo, f.bar));
    }
}
```
 
### Skipping and Limiting

```java
Stream.iterate(new Integer(1), i -> new Integer(i.val + 1)).skip(2).limit(2);
```

### Removing Duplicates

```java
Stream.of(1, 2).distinct();
```

### Sorting
Reference: [Stream.sorted(Comparator)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#sorted-java.util.Comparator-)

```java
import java.util.Arrays;
import java.util.Comparator;
import java.util.List;
import java.util.stream.Collectors;

class Foo {    
    List<String> strings;
    Integer val;

    Foo(Integer val, List<String> strings) {
        this.val = val;
        this.strings = strings;
    }

    void foo(){
        // Initial data
        Foo foo = new Foo(4, Arrays.asList("foo"));
        Foo fooBar = new Foo(3, Arrays.asList("foo", "bar"));
        Foo fooBarBaz = new Foo(2, Arrays.asList("foo", "bar", "baz"));
        Foo fooBarBazQx = new Foo(1, Arrays.asList("foo", "bar", "baz", "qux"));
        List<Foo> fooList = Arrays.asList(foo, fooBarBazQx, fooBarBaz, fooBar);

        // Sort by value
        List<Foo> sorted = fooList.stream()
                                  .sorted(Comparator.comparing(Foo::getVal))
                                  .collect(Collectors.toList());
        System.out.println(sorted);
        // [[foo, bar, baz, qux], [foo, bar, baz], [foo, bar], [foo]]
        
        // Sort by length of strings
        sorted = fooList
            .stream()
            .sorted(Comparator.comparing(Foo::getStrings, 
                                         Comparator.comparing(List::size)))
            .collect(Collectors.toList());
        System.out.println(sorted);
        // [[foo], [foo, bar], [foo, bar, baz], [foo, bar, baz, qux]]        
    }

    List<String> getStrings() {
        return strings;
    }

    Integer getVal() {
        return val;
    }
    
    public String toString() {
        return strings.toString();
    }
}
```

## Terminal Operations
### Simple Reductions
#### Number of Elements

```java
// Do not try on an infinite stream!
Stream.of(1, 2).count(); // 2
```

#### Finding the minimum and the maximum
```java
import java.util.Comparator;
import java.util.stream.Stream;

class Foo implements Comparable<Foo> {
    int foo;
    Foo(int foo) {this.foo = foo;}
    void foo() {
        Stream<Foo> fooStream = 
            Stream.of(new Foo(1), new Foo(2), new Foo(3), new Foo(4));
        // Using Comparator.naturalOrder
        fooStream.min(Comparator.naturalOrder()); // Optional[Foo[1]]
    }
    @Override
    public int compareTo(Foo o) {
        return Integer.compare(foo, o.foo);
    }
}
```

#### Finding the maximum

```java
import java.util.Comparator;
import java.util.stream.Stream;

class Foo {
    int foo;
    Foo(int foo) {this.foo = foo;}
    void foo() {
        Stream.of(new Foo(1), new Foo(2))
               .max(Comparator.comparing(f -> f.foo)); // Optional[Foo[2]]
    }
}
```

#### Example demonstrating various possibilities for generating Comparator arguments  

```java
import java.util.Comparator;
import java.util.stream.Stream;

class Foo {
    void foo() {
        // All of the examples above return Optional[foo]
        // Using Comparator.comparing
        Stream.of("foo", "bar")
            .max(Comparator.comparing(s -> s.toLowerCase()));
        
        // Using Method References
        Stream.of("foo", "bar").max(String::compareToIgnoreCase);
        
        // Providing custom method definition with Lambdas
        Stream.of("foo", "bar").max((o1, o2) -> o1.compareToIgnoreCase(o2));
    }
}
```

### Querying Streams

```java
// findAny
Stream.of(1, 2, 3 ,4).findAny();  // Optional[1|2|3|4]

// anyMatch
Stream.of(new Foo(1), new Foo(2), new Foo(3), new Foo(4))
    .anyMatch(f -> f.foo < 2); // true
```

### Collecting Streams
#### Collecting Into Arrays

```java
Stream.of(1).toArray(Integer[]::new); // Integer[]{1}
```

#### Collecting Using Collectors

```java
Stream.of(1).collect(Collectors.toList()); // List<Integer>
```

## Parallel Streams
- It is your responsibility to ensure any operations you pass to parallel streams are safe to execute in parallel
- Some operations can be more effectively parallelised when ordering is dropped using `Stream.unordered()`

```java
import static java.util.concurrent.ThreadLocalRandom.current;
import static java.lang.Integer.MAX_VALUE;
import java.util.stream.Stream;

/**
* A program demonstrating the execution times of parallel and unordered streams
* by finding number of distinct integers in a randomly generated stream of ints
*/
class App {
    public static void main(String[] args) {
        int div = 32;
        long start;
        
        // Initial..
        start = System.currentTimeMillis();
        Stream.generate(() -> current().nextInt(0, Math.floorDiv(MAX_VALUE, 4)))
              .limit(MAX_VALUE / div).distinct().count();
        log(start, System.currentTimeMillis());
        // Took 71467 milliseconds.

        // unordered
        start = System.currentTimeMillis();
        Stream.generate(() -> current().nextInt(0, Math.floorDiv(MAX_VALUE, 4)))
              .limit(MAX_VALUE / div).unordered().distinct().count();
        log(start, System.currentTimeMillis());
        // Took 59521 milliseconds.

        // parallel
        start = System.currentTimeMillis();
        Stream.generate(() -> current().nextInt(0, Math.floorDiv(MAX_VALUE, 4)))
              .limit(MAX_VALUE / div).parallel().distinct().count();
        log(start, System.currentTimeMillis());
        // Took 47279 milliseconds.

        // unordered and parallel
        start = System.currentTimeMillis();
        Stream.generate(() -> current().nextInt(0, Math.floorDiv(MAX_VALUE, 4)))
              .limit(MAX_VALUE / div).unordered().parallel().distinct().count();
        log(start, System.currentTimeMillis());
        // Took 31375 milliseconds.
    }

    static void log(long start, long end) {
        println(join(" ", "Took", valueOf((end - start)), "milliseconds."));
    }
}
```

## Examples
### Random Walk

```java
import java.util.List;
import java.util.stream.Collectors;
import static java.util.concurrent.ThreadLocalRandom.current;
import static java.util.stream.IntStream.iterate;

class RandomWalk {
    void randomWalk() {
        List<Integer> randomWalk = 
            iterate(0, i -> current().nextBoolean() ? i - 1 : i + 1)
            .boxed()
            .limit(10)
            .collect(Collectors.toList()); // [0, -1, 0, 1, 2, 3, 4, 5, 4, 5]
    }
}
```

### Generating a Sequence of Random Numbers
```java
// Generate 1000 random numbers between 0 and 100
// Store in a LinkedList
IntStream.generate(() -> ThreadLocalRandom.current().nextInt(0, 100))
         .limit(1000)
         .boxed()
         .collect(Collectors.toCollection(LinkedList::new));
``` 

## References
- [Stream API Documentation](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html)
- [Processing Data with Java SE 8 Streams, Part 1](https://www.oracle.com/technetwork/articles/java/ma14-java-se-8-streams-2177646.html)