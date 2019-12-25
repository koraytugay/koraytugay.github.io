---
layout: default
title:  "Collectors"
---

# Collectors
{:.no_toc}

* TOC
{:toc}

## Overview
- Found in [java.util.stream.Collectors](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html)
- Provides lots of static factory methods that returns [java.util.stream.Collector](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collector.html)s

### Cheat Sheet

```xml
<!-- Collecting into Single Values -->
Collectors.counting()
Collectors.maxBy(Comparator)
Collectors.summingInt(ToIntFunction)
Collectors.averagingInt(ToIntFunction)
Collectors.summarizingInt(ToIntFunction)

<!-- Collecting into Collections -->
Collectors.toCollection(Supplier)
Collectors.toList()
Collectors.toSet()
Collectors.toMap(Function, Function)
Collectors.toMap(Function, Function, BinaryOperator)

<!-- Grouping and Partitioning-->
Collectors.groupingBy(Function)
Collectors.groupingBy(Function, Collector)
Collectors.partitioningBy(Predicate)
```

## Aggregating a Stream
### Counting Number of Elements in a Stream

```java
Stream.of(new Integer(1), new Integer(2)).collect(Collectors.counting()); // 2
```

### Collecting by Maximum or Minimum

```java
class Foo {
    // constructor, getter
    int foo;
}

Stream.of(new Foo(1), new Foo(2)).collect(
  Collectors.maxBy(Comparator.comparing(Foo::getFoo))); // Optional[Foo[2]]
```

### Collecting to a sum
[Collectors.summingInt(ToIntFunction)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html#summingInt-java.util.function.ToIntFunction-)

```java
import java.util.stream.Collectors;
import java.util.stream.Stream;

class Foo {
    
    int foo;

    Foo(int foo) {this.foo = foo;}

    void foo() {
        Stream.of(new Foo(1), new Foo(2))
              .collect(Collectors.summingInt(Foo::getFoo)); // 3
    }

    int getFoo() {
        return foo;
    }
}
```

### Collecting to average
[Collectors.averagingInt(ToIntFunction)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html#averagingInt-java.util.function.ToIntFunction-)
```java
import java.util.stream.Collectors;
import java.util.stream.Stream;

class Foo {
    int foo;

    Foo(int foo) {this.foo = foo;}

    void foo() {
        Stream.of(new Foo(1), new Foo(2))
              .collect(Collectors.averagingInt(Foo::getFoo)); // 1.5
    }

    int getFoo() {
        return foo;
    }
}
```

## Collecting into Collections
### Collecting into an ArrayList
[Collectors.toCollection(Supplier)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html#toCollection-java.util.function.Supplier-)

```java
import java.util.ArrayList;
import java.util.stream.Collectors;
import java.util.stream.Stream;

class Foo {
    int foo;

    Foo(int foo) {this.foo = foo;}

    void foo() {
        Stream.of(new Foo(1), new Foo(2))
              .collect(Collectors.toCollection(ArrayList::new)); 
              // [Foo[1], Foo[2]]
    }
}
```

See also: [Collectors.toList()](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html#toList--) and [Collectors.toSet()](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html#toSet--) 

## Grouping Streams
### Collecting into Maps
[Collectors.toMap(Function, Function)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html#toMap-java.util.function.Function-java.util.function.Function-)

```java
import java.util.stream.Stream;
import java.util.stream.Collectors;
import java.util.Map;

class Foo {
    int foo;
    String bar;

    Foo(int foo, String bar){this.foo = foo; this.bar = bar;}

    void foo() {
        Stream.of(new Foo(1, "foo"), new Foo(2, "bar"), new Foo(3, "baz"))
              .collect(Collectors.toMap(f -> f.foo, f -> f.bar)); 
              // Map<Integer, String> : {1=foo, 2=bar, 3=baz}
    }
}
```

#### Collecting into Maps Considering Duplicate Keys - Overwriting or Keeping Existing
[Collectors.toMap(Function, Function, BinaryOperator)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html#toMap-java.util.function.Function-java.util.function.Function-java.util.function.BinaryOperator-)

```java
class Foo {
    int foo;
    String bar;
}

// Note the 2 instances with same id:
Stream<Foo> fooStream = 
    Stream.of(new Foo(1, "foo"), new Foo(2, "bar"), new Foo(1, "baz"));

Map<Integer, String> fooMap =
fooStream.collect(Collectors.toMap(f -> f.foo,
                                   f -> f.bar,
                                   // Overwrite the existing value
                                   (exitingValue, newValue) -> newValue));
                                   // {1=baz, 2=bar}

// This will throw an Exception in runtime:
Map<Integer, String> fooMap = 
    fooStream.collect(Collectors.toMap(f -> f.foo, f -> f.bar));
// java.lang.IllegalStateException: Duplicate key foo
```

#### Collecting into Maps Considering Duplicate Keys - Keeping All Values
[static toMap(Function, Function, BinaryOperator) // Collector](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html#toMap-java.util.function.Function-java.util.function.Function-java.util.function.BinaryOperator-)

```java
import java.util.stream.Stream;
import java.util.stream.Collectors;
import java.util.Map;
import java.util.Set;
import java.util.HashSet;
import java.util.Collections;

class Foo {
    int foo;
    String bar;

    Foo(int foo, String bar){this.foo = foo; this.bar = bar;}

    void foo() {
        Stream<Foo> fooStream = 
            Stream.of(new Foo(1, "foo"), new Foo(2, "bar"), new Foo(1, "baz"));
        Map<Integer, Set<Foo>> fooMap =  fooStream.collect(Collectors.toMap(
            f -> f.foo,
            f -> Collections.singleton(f),
            (existingValue, newValue) -> {
                Set<Foo> fooSet = new HashSet<>(existingValue);
                fooSet.addAll(newValue);
                return fooSet;
            })
        ); 
        // {
        //     1 = [Foo[1, baz], Foo[1, foo]], 
        //     2 = [Foo[2, bar]]
        // }
    }

    @Override
    public String toString() {
        return "Foo[" + foo + ", " + bar + "]";
    }
}
```

### Collecting by Grouping
[Collectors.groupingBy(Function)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html#groupingBy-java.util.function.Function-)

```java
import java.util.stream.Collectors;
import java.util.stream.Stream;

class Foo {
    int foo;

    Foo(int foo) {this.foo = foo;}

    void foo() {
        Stream.of(new Foo(1), new Foo(1), new Foo(2), new Foo(2))
              .collect(Collectors.groupingBy(f -> f.foo));
        // {
        //     1=[Foo[1], Foo[1]],
        //     2=[Foo[2], Foo[2]]
        // }
    }
}
```

#### Collecting with Nested Grouping
[Collectors.groupingBy(Function, Collector)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html#groupingBy-java.util.function.Function-java.util.stream.Collector-)

```java
import java.util.stream.Collectors;
import java.util.stream.Stream;

class Foo {
    int foo;
    String bar;

    Foo(int foo, String bar) {this.foo = foo; this.bar = bar;}
    
    void foo() {
        Stream<Foo> foos = Stream.of(new Foo(1, "foo"), new Foo(1, "bar"), 
            new Foo(2, "baz"), new Foo(2, "qux"));
        
        // Map<Integer, Map<String, List<Foo>>>
        foos.collect(Collectors.groupingBy(f -> f.foo, 
                     Collectors.groupingBy(f -> f.bar)));
        // {
        //     1={
        //           bar=[Foo[1, bar]], 
        //           foo=[Foo[1, foo]]}, 
        //     2={
        //           qux=[Foo[2, qux]], 
        //           baz=[Foo[2, baz]]}
        // }
    }
    
}
```

```java
class Foo {int foo;}
Stream.of(new Foo(42), new Foo(42), new Foo(84))
      .collect(Collectors.groupingBy(f -> f.foo, Collectors.counting()));
      // Map<Long, Integer> : {42=2, 84=1}
```

## Partitioning streams
[static Map<Boolean, List> partitioningBy(Predicate)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html#partitioningBy-java.util.function.Predicate-)

```java
Stream.iterate(1, i -> i + 1)
      .limit(10)
      .collect(Collectors.partitioningBy(i -> i < 5)); 
      // {false=[5, 6, 7, 8, 9, 10], true=[1, 2, 3, 4]}
``` 