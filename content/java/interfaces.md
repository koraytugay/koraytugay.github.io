---
layout: default
title:  "Interfaces"
---

# Interfaces
{:.no_toc}

* TOC
{:toc}

## Overview
- All methods of an interface are `public` by default
  - An implementing class must also declare methods as `public`
- All methods of an interface without an implementation are `abstract` by default
  - An implementing class must either be `abstract` itself or must provide an implementation
- Any variable defined in an interface is automatically `public static final`

```java
interface Foo {
    int foo();     // public abstract
    int FOO = 42;  // public static final
}
class FooImpl implements Foo {
    @Override
    public int foo() {  // must be public!
        return FOO;     // 42
    }
}
```

## Default Methods in Interfaces
- Interfaces can have non-abstract instance methods
  - Such methods must have the `default` modifier
- Classes implementing interfaces do not need to provide implementation for default methods
- Used extensively in the Java 8 standard library
  - Examples from the Java Standard Library: [Collection.removeIf(Predicate)](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html#removeIf-java.util.function.Predicate-), [List.sort(Comparator)](https://docs.oracle.com/javase/8/docs/api/java/util/List.html#sort-java.util.Comparator-)

```java
interface Foo {
    default int foo() {
        return 42;
    }
}
class FooImpl implements Foo {
    // no need to override foo!
    void bar() {
        Foo f = new FooImpl();
        f.foo(); // 42
    }
}
```

- default methods are `public` by default
  - default methods can be marked `public` although it is simply redundant
  - default methods can not be marked as `private` or `protected`
- default methods can not be `static`, `final` or `abstract`

### Default Method Conflicts
- Any class wins over any interface. If there is either an abstract or a concrete method in the inheritance tree of the class, any default methods in any Interface is ignored.
- If two Interfaces are competing to provide a default method implementation to a class and if one of the interfaces extends the other, the subtype (the extending Interface, the child) wins
- If the above two rules do not give an answer, the class must provide its own implementation

#### Examples
- If a class inherits the same method from multiple interfaces, an implementation must be provided
  - Does not matter if both methods are default or only one of them is

```java
interface Foo {
    default int foo() {
        return 42;
    }
}
interface Bar {
    default int foo() {
        return -42;
    }
}
class FooBar implements Foo, Bar {
    public int foo() {
        return 0;
    }
    // It would not make any difference if either Foo.foo or Bar.foo was abstract
}
```

- The implementing class can still delegate to a default implementation of one of the interfaces

```java
class FooBar implements Foo, Bar {
    public int foo() {
        return Foo.super.foo(); // super weird syntax!
    }
}
```

- If a class extends another concrete class and implements an interface, the super class implementation overrides the default implementation in the inherited interface

```java
interface Foo {
    default int foo() {
        return 42;
    }
}
class Bar {
    public int foo() {
        return -42;
    }
}
class FooBar extends Bar implements Foo  {
    void fooBar() {
        foo(); // -42
    }
}
```

## Static Methods in Interfaces
### Calling a static method of a not-inherited interface
- When invoking a static interface method, the methods type (interface name) must be included in the invocation

```java
interface Bar {
    static void bar() {}
}

class Foo {
    void foo() {
        Bar.bar();  // No other way
    }
}
```

### Calling a static method of an inherited interface

```java
interface Foo {
    static void foo() {}
}

class FooImpl implements Foo {
    void fooTest() {
        // This is not allowed:
        // foo();
        
        // Correct way is:
        Foo.foo();  // No other way
    }
}
```
- The only reason the [java.nio.file.Paths](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Paths.html) class exists is to define static methods that return instances implementing the [java.nio.file.Path](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html) interface. 
With the support for static methods the such verbosity can be eliminated.
  - [java.nio.file.Files](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html) also provides similar static methods
- Example from the Java Standard Library: [Comparator.naturalOrder()](https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html#naturalOrder--)

### Inheritance in Static Methods in Interfaces
- Static methods are not inherited in interface inheritance trees

```java
interface Foo {
    static void foo() {}
}

interface Bar extends Foo {}

// This will not compile:
// Bar.foo();
```

- Static methods are visible (though can not be overridden, can only be hidden) in class inheritance trees

```java
class Foo {
    static void foo() {}
}

class Bar extends Foo {}

// This compiles and runs fine:
Bar.foo();
```

## Functional Interfaces
- An interface with a single abstract method
  - Static and default methods do not count
  - Methods inherited from class `java.lang.Object` do not count

```java
@FunctionalInterface
interface Foo {
    // single abstract method
    void foo();
    // equals method here is fine, it is inherited from the Object class 
    boolean equals(Object o);
}

class FooImpl implements Foo {
    @Override
    public void foo(){}
    // Implementing Foo does not mean equals method must be implemented
}
```

### Functional Interfaces in JDK 8
- Found in the [java.util.function](https://docs.oracle.com/javase/8/docs/api/java/util/function/compact1-package-summary.html) package
- A set of general purpose interfaces
- Used extensively in Streams API
- Some of them are type specific, some of them are generic typed

<table>
  <tr>
    <th>Interface</th>
    <th>SAM</th>
    <th>Arguments</th>
    <th>Returns</th>
<tr>
    <td>Predicate</td>
    <td>test</td>
    <td>T</td>
    <td>boolean</td>

</tr>
<tr>
    <td>BiPredicate</td>
    <td>test</td>
    <td>T, U</td>
    <td>boolean</td>

</tr>
<tr>
    <td>Consumer</td>
    <td>accept</td>
    <td>T</td>
    <td>void</td>

</tr>
<tr>
    <td>BiConsumer</td>
    <td>accept</td>
    <td>T, U</td>
    <td>void</td>
</tr>
<tr>
    <td>Supplier</td>
    <td>get</td>
    <td></td>
    <td>T</td>
</tr>
<tr>
    <td>Function</td>
    <td>apply</td>
    <td>T</td>
    <td>R</td>
</tr>
<tr>
    <td>UnaryOperator</td>
    <td>apply</td>
    <td>T</td>
    <td>T</td>
</tr>
<tr>
    <td>BiFunction</td>
    <td>apply</td>
    <td>T, U</td>
    <td>R</td>
</tr>
<tr>
    <td>BinaryOperator</td>
    <td>apply</td>
    <td>T, T</td>
    <td>T</td>
</tr>
</table>
<sup>1</sup> Single Abstract Method

#### Primitive Specializations of Functional Interfaces in JDK 8
<small>__* Purely Primitive Interfaces are Printed in Bold__</small>
<table>
    <tr><th>Interface</th><th>SAM</th><th>Arguments</th><th>Returns</th>
    <tr><td align="center" colspan="4">Predicate</td></tr>
    <tr><td><strong>IntPredicate</strong></td><td>test</td><td>int</td><td>boolean</td></tr>
    <tr><td><strong>LongPredicate</strong></td><td>test</td><td>long</td><td>boolean</td></tr>
    <tr><td><strong>DoublePredicate</strong></td><td>test</td><td>double</td><td>boolean</td></tr>
    <tr><td align="center" colspan="4">Consumer</td></tr>
    <tr><td><strong>IntConsumer</strong></td><td>accept</td><td>int</td><td>void</td></tr>
    <tr><td><strong>LongConsumer</strong></td><td>accept</td><td>long</td><td>void</td></tr>
    <tr><td><strong>DoubleConsumer</strong></td><td>accept</td><td>double</td><td>void</td></tr>
    <tr><td align="center" colspan="4">BiConsumer</td></tr>
    <tr><td>ObjIntConsumer</td><td>accept</td><td>T, int</td><td>void</td></tr>
    <tr><td>ObjLongConsumer</td><td>accept</td><td>T, long</td><td>void</td></tr>
    <tr><td>ObjDoubleConsumer</td><td>accept</td><td>T, double</td><td>void</td></tr>
    <tr><td align="center" colspan="4">Supplier</td></tr>
    <tr><td><strong>IntSupplier</strong></td><td>getAsInt</td><td></td><td>int</td></tr>
    <tr><td><strong>LongSupplier</strong></td><td>getAsLong</td><td></td><td>long</td></tr>
    <tr><td><strong>DoubleSupplier</strong></td><td>getAsDouble</td><td></td><td>double</td></tr>
    <tr><td><strong>BooleanSupplier</strong></td><td>getAsBoolean</td><td></td><td>boolean</td></tr>
    <tr style="display:none;"></tr>
    <tr><td align="center" colspan="4">Function</td></tr>
    <tr><td>IntFunction</td><td>apply</td><td>int</td><td>R</td></tr>
    <tr><td>LongFunction</td><td>apply</td><td>long</td><td>R</td></tr>
    <tr><td>DoubleFunction</td><td>apply</td><td>double</td><td>R</td></tr>
    <tr><td>ToIntFunction</td><td>applyAsInt</td><td>T</td><td>int</td></tr>
    <tr><td>ToLongFunction</td><td>applyAsLong</td><td>T</td><td>long</td></tr>
    <tr><td>ToDoubleFunction</td><td>applyAsDouble</td><td>T</td><td>double</td></tr>
    <tr><td><strong>IntToDoubleFunction</strong></td><td>applyAsDouble</td><td>int</td><td>double</td></tr>
    <tr><td><strong>IntToLongFunction</strong></td><td>applyAsLong</td><td>int</td><td>long</td></tr>
    <tr><td><strong>LongToIntFunction</strong></td><td>applyAsInt</td><td>long</td><td>int</td></tr>
    <tr><td><strong>LongToDoubleFunction</strong></td><td>applyAsDobule</td><td>long</td><td>double</td></tr>
    <tr><td><strong>DoubleToIntFunction</strong></td><td>applyAsInt</td><td>double</td><td>int</td></tr>
    <tr><td><strong>DoubleToLongFunction</strong></td><td>applyAsLong</td><td>double</td><td>long</td></tr>
    <tr style="display:none;"></tr>
    <tr><td align="center" colspan="4">UnaryOperator</td></tr>
    <tr><td><strong>IntUnaryOperator</strong></td><td>applyAsInt</td><td>int</td><td>int</td></tr>
    <tr><td><strong>LongUnaryOperator</strong></td><td>applyAsLong</td><td>long</td><td>long</td></tr>
    <tr><td><strong>DoubleUnaryOperator</strong></td><td>applyAsDouble</td><td>double</td><td>double</td></tr>
    <tr><td align="center" colspan="4">BiFunction</td></tr>
    <tr><td>ToIntBiFunction</td><td>applyAsInt</td><td>T, U</td><td>int</td></tr>
    <tr><td>ToLongBiFunction</td><td>applyAsLong</td><td>T, U</td><td>long</td></tr>
    <tr><td>ToDoubleBiFunction</td><td>applyAsDouble</td><td>T, U</td><td>double</td></tr>
    <tr><td align="center" colspan="4">BinaryOperator</td></tr>
    <tr><td><strong>IntBinaryOperator</strong></td><td>applyAsInt</td><td>int, int</td><td>int</td></tr>
    <tr><td><strong>LongBinaryOperator</strong></td><td>applyAsLong</td><td>long, long</td><td>long</td></tr>
    <tr><td><strong>DoubleBinaryOperator</strong></td><td>applyAsDouble</td><td>double, double</td><td>double</td></tr>
</table>

### Predicate Default Methods
Predicates can be chained with `and`, `or` and `negate` as seen below.

```java
List<String> strings = new ArrayList<>(Arrays.asList("foo", "foof", "boo", "boob"));
Predicate<String> startsWithF = s -> s.toLowerCase().charAt(0) == 'f';
Predicate<String> endsWithF = s -> s.toLowerCase().charAt(s.length() - 1) == 'f';

strings.removeIf(startsWithF.and(endsWithF).negate()); // [foof]
```

### Chaining Consumers and Functions with `andThen`
You can __not__ chain `Consumer`s with `BiConsumer`s, but you __can__ chain `Function`s with `BiFunction`s.
```java
// An example of chaining a BiFunction with a Function
BiFunction<String, Integer, String> biFunction = (s, integer) -> s + integer;
Function<String, String> function = s -> s.toLowerCase();
biFunction.andThen(function).apply("APPEND", 42); // append42

// An example of chaining Consumers
Consumer<StringBuilder> trimmer = s -> s.deleteCharAt(0);
Consumer<StringBuilder> doubler = s -> s.append(s.toString());
StringBuilder sb = new StringBuilder("kkoray");
trimmer.andThen(doubler).accept(sb); // koraykoray
```

## Examples
### BiPredicate Example
```java
import java.util.function.BiPredicate;

class FooBar {
    static class Foo {int foo = 42;}
    static class Bar {int bar = 42;}
    public static <T, U> boolean foo(T t, U u, BiPredicate<T,U> biPredicate) {
        return biPredicate.test(t, u);
    }
    public static void main(String[] args) {
        foo(new Foo(), new Bar(), (foo, bar) -> foo.foo == 42 && bar.bar == 42); // true
    }
}
```

## References
- [The Java™ Tutorials - Interfaces](https://docs.oracle.com/javase/tutorial/java/IandI/createinterface.html)
