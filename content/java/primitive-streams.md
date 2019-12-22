---
layout: default
title:  "Primitive Streams"
---

# Primitive Streams
{:.no_toc}

## Overview 
- [IntStream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/IntStream.html)
- [LongStream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/LongStream.html)
- [DoubleStream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/DoubleStream.html)

### Cheat Sheet
```xml 
<!-- Creating Primitive Streams -->
IntStream.of(int...)
IntStream.range(int, int)
IntStream.generate(IntSupplier)
IntStream.iterate(int, IntUnaryOperator)
Random.ints()
Random.ints(int, int)

<!-- Intermediate Operations on Primitive Streams -->
IntStream.flatMapToInt(ToIntFunction)
IntStream.mapToObj(IntFunction)
IntStream.boxed()

<!-- Terminal Operations on Primitive Streams -->
is.toArray();
is.min();
is.max();
is.average();
is.sum();
is.summaryStatistics();
```

## Creating Primitive Streams
### Creating Primitive Streams Using [Int | Long | Double]Stream Methods
[IntStream.of(int...)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/IntStream.html#of-int...-)
```java
import java.util.stream.IntStream;

class Foo {    
    void foo() {
        IntStream.of(1, 2); // IntStream with values 1, 2
    }
}
```

[IntStream.range(int, int)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/IntStream.html#range-int-int-)
```java
import java.util.stream.IntStream;

class Foo {    
    void foo() {
        IntStream.range(1, 6); // IntStream with values 1, 2, 3, 4, 5
    }
}
```

[IntStream.generate(IntSupplier)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/IntStream.html#generate-java.util.function.IntSupplier-)
```java
import java.util.stream.IntStream;

class Foo {    
    void foo() {
        IntStream.generate(() -> 1); // // An infinite IntStream of 1s
    }
}
```

[IntStream.iterate(int, IntUnaryOperator)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/IntStream.html#iterate-int-java.util.function.IntUnaryOperator-)
```java
import java.util.stream.IntStream;

class Foo {    
    void foo() {
        IntStream.iterate(1, i -> i * 2); // An infinite IntStream of: 1, 2, 4, 8, 16...
    }
}
```

### Creating Primitive Streams Using the Random class
[Random.ints()](https://docs.oracle.com/javase/8/docs/api/java/util/Random.html#ints--)
```java
import java.util.Random;;

class Foo {
    void foo() {
        final Random random = new Random();
        random.ints();
    }
}
```

## Intermediate Operations on Primitive Streams
### FlatMapping to Primitive Type Streams
[Stream.flatMapToInt(Function)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#flatMapToInt-java.util.function.Function-)
```java
import java.util.stream.Stream;
import java.util.stream.IntStream;

class Foo {    
    int foo;
    int bar;
    
    Foo(int foo, int bar){this.foo = foo; this.bar = bar;}

    void foo() {
        Stream<Foo> foo = Stream.of(new Foo(1, 2), new Foo(3, 4));
        foo.flatMapToInt(f -> IntStream.of(f.foo, f.bar)); // IntStream with values: 1, 2, 3, 4
    }
}
```

### Mapping to Generic Stream
[IntStream.mapToObj(IntFunction)](https://docs.oracle.com/javase/8/docs/api/java/util/stream/IntStream.html#mapToObj-java.util.function.IntFunction-)
```java
import java.util.stream.Stream;
import java.util.stream.IntStream;


class Foo {
    int val;
    
    Foo(int val){this.val = val;}
    
    void foo() {
        IntStream is = IntStream.rangeClosed(1, 2);
        is.mapToObj(i -> new Foo(i)); // A Stream<Foo> with 2 instances: Foo[1] and Foo[2]
        // Another possibility: is.mapToObj(Foo::new);
    }
}
```
Also see: [IntStream.boxed()](https://docs.oracle.com/javase/8/docs/api/java/util/stream/IntStream.html#boxed--)
## Terminal Operations on Primitive Streams
<br />
<table>
<tr><th>Method</th><th>Return Type</th><th>Reference Stream Equivalent</th></tr>
<tr><td>toArray()</td><td>int[]</td><td></td></tr>
<tr><td>count()</td><td>long</td><td>Stream.count()</td></tr>
<tr><td>min()</td><td>OptionalInt</td><td>Stream.min(Comparator)</td></tr>
<tr><td>max()</td><td>OptionalInt</td><td>Stream.max(Comparator)</td></tr>
<tr><td>average()</td><td>OptionalDouble</td><td>Stream.collect(Collectors.averagingInt(ToIntFunction))</td></tr>
<tr><td>sum()</td><td>int</td><td>Stream.collect(Collectors.summingInt(ToIntFunction))</td></tr>
<tr><td>summaryStatistics()</td><td>IntSummaryStatistics</td><td>Stream.collect(Collectors.summarizingInt(ToIntFunction))</td></tr>
<tr><td>findAny()</td><td>OptionalInt</td><td>Stream.findAny()</td></tr>
<tr><td>findFirst()</td><td>OptionalInt</td><td>Stream.findFirst()</td></tr>
</table>