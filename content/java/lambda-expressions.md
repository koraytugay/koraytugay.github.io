---
layout: default
title:  "Lambda Expressions"
---

# Lambda Expressions
{:.no_toc}

* TOC
{:toc}

## Definition
Implementation of the Single Abstract Method (SAM) of a Functional Interface.

```java
BinaryOperator<Long> longAdder = (Long x, Long y) -> {return x + y;};
```

This line of code does not add two `long` values. `longAdder` is the implemention of the `apply` method in `BinaryOperator` interface. When the `apply` method is called on `longAdder`, two `long` values will be summed.

```java
longAdder.apply(Long.valueOf(1), Long.valueOf(2)); // Long[3]
```

## Syntax

```
(parameters) -> {lambda-body} // -> is the lambda operator
```
- Lambda expressions are not allowed to declare the Exceptions they throw

### Syntax for Parameters
- Parameters must be surrounded by parenthesis

```java
@FunctionalInterface
interface Foo {
    void foo();
}

@FunctionalInterface
interface Baz {
    void baz(Integer i, String s);
}

class App {
    void fooBarBaz() {
        Foo foo = () -> {};
        Baz baz = (Integer i, String s) -> {};
    }
}
``` 

- Parameters can be declared without types
  - Either all parameter types must be omitted or none

```java
@FunctionalInterface
interface Baz {
    void baz(Integer i, String s);
}

class App {
    void fooBarBaz() {
        Baz baz = (i, s) -> {};
        // This is not allowed:
        // Baz baz = (Integer i, s) -> {};
    }
}
``` 

- Parameters can be modified in the same way as method parameters
  - They can be final
  - They can be annotated

```java
@FunctionalInterface
interface Baz {
    void baz(Integer i, String s);
}

class App {
    void fooBarBaz() {
        Baz baz = (final Integer i, final String s) -> {};
    }
}
```

- Parenthesis can be omitted for single parameter lambdas
  - If parenthesis are omitted, type information for the single parameter can not be included
  - If parenthesis are omitted, the single parameter can not have any modifiers

```java
@FunctionalInterface
interface FooTest {
    void bar(Integer i);
}
class App {
    void fooBarBaz() {
        FooTest bar = i -> {};
        // This is not allowed:
        // FooTest bar = Integer i -> {}; 
    }
}
```

### Syntax for Lambda-Body
- Method body must be enclosed by curly braces

```java
@FunctionalInterface
interface Foo {
    void foo(String s);
}

@FunctionalInterface
interface FooTest {
    Integer bar(String s);
}

class App {
    void fooBar() {
        Foo foo = s -> {System.out.println(s);};
        FooTest bar = s -> {return -1;};
    }
}
```

- Method body can be empty for lambdas with `void` return

```java
@FunctionalInterface
interface Foo {
    void foo(String s);
}

class App {
    void fooBar() {
        Foo foo = s -> {};
    }
}
```

- Enclosing curly braces can be omitted in case body consists of a single expression
  - In case curly braces are omitted, `return` keyword must be omitted as well

```java
@FunctionalInterface
interface Foo {
    void foo(String s);
}

@FunctionalInterface
interface Bar {
    int bar(String s);
}

class App {
    void fooBar() {
        Foo foo = s -> System.out.println(s);
        Bar bar = s -> -1; // return -1;
    }
}
```

- Returning `null` or chained method calls are fine when curly braces are omitted

```java
@FunctionalInterface
interface Foo {
    void foo(String s);
}

@FunctionalInterface
interface Bar {
    Integer bar(String s);
}

class App {
    void fooBar() {
        Foo foo = s -> new StringBuilder().append(s).append(s).append(s);
        Bar bar = s -> null; // return null;
    }
}
```

- Calling another method is just fine

```java
@FunctionalInterface
interface Foo {
    Integer foo(int i);
}

class App {
    void foo() {
        Foo foo = i -> Math.abs(i); // return Math.abs(i);
    }
}
```

## Target Typing and Type Inference
- Type of a Lambda Expression is _inferred_ from the context and is called the __Target Type__
  - A Lambda Expression can only exist in a context where its Target Type is a Functional Interface

```java
Runnable r  = () -> {}; // Runnable here is the Target Type
```

### Lambda Expression Contexts
- Method / Constructor Argument

```java
new Thread(() -> {});
```

- Variable Assignment

```java
Runnable r = () -> {};
```

- Return Statement

```java
Runnable runnable() {
    return () -> {};
}
```

- Ternary Conditional Expression

```java
Runnable r = aBoolean ? () -> {} : () -> {};
```

- Body of Another Lambda Expression
  - Pretty much same with method return statement

```java
Supplier<Runnable> runnableSupplier = () -> () -> {};
```

- With Explicit Casting

```java
Object o = (Runnable) () -> {};
```

- Array Initializers

```java
Runnable[] arr = new Runnable[]{() -> {}, () -> {}};
```

## Scoping Rules
- No inheritance from super types

```java
@FunctionalInterface
interface Foo {
    int FOO_CONST = -1; 
    int foo();
}

class FooTest {
    void fooTest() {
        // Not allowed: Foo foo = () -> {return FOO_CONST;};        
        Foo foo = () -> Foo.FOO_CONST;
    }
}
```

- It is illegal to redeclare a locale variable in lambda

```java
@FunctionalInterface
interface Foo {
    int foo(int i);
}

class FooTest {
    void fooTest() {
        int i = -1;
        // Not allowed: Foo foo = (i) -> -1;
        // Not allowed: Foo foo = (k) -> {int i = -5; return k;};
        // This is fine:
        Foo foo = (k) -> {k = 10; return k;};
    }
}
```

- Shadowing instance fields is possible
  - Also in parameter list

```java
@FunctionalInterface
interface Foo {
    int foo();
}

class FooTest {

    int i = -1;

    void fooTest() {
        // Shadows the instance field i, returns 10 not -1:
        Foo foo = () -> {
            int i = 10;
            return i;
        };
    }
}
```

- `this` refers to enclosing object

```java
@FunctionalInterface
interface Foo {
    int foo();
}

class FooTest {
    int i = -1;
    void fooTest() {
        Foo foo = () -> {
            int i = 1;
            return this.i;  // returns -1, not 1!
        };
    }
}
```

- Accessing private methods is fine

```java
import java.util.function.IntSupplier;

class Foo {
    private int foo() {
        return 42;
    }
    public int bar() {
        // Compiles fine
        return ((IntSupplier) () -> foo()).getAsInt();
    }
}
```

## Value Capture
- Lambda Expressions can capture _effectively final_ variables

```java
// aLong here is effectively final, this code compiles fine
long aLong = 1L;
LongUnaryOperator op = l -> aLong;

long anotherLong = 1L;
anotherLong = 2L; // not effectively final!
// This code will not compile
// LongUnaryOperator op = l -> anotherLong;
```

- Captured local variables can not be modified 

```java
void foo() {
    int i = 10;
    // Will not compile since i is modified
    // IntSupplier is = () -> i++;    
}
```

- Captured instance variables can be modified

```java
class Foo {
    int i;
    void foo() {
        // fine since i is an instance variable
        IntSupplier is = () -> i++;
    }
}
```

- If instance variable is explicitly declared final, it can not be modified

```java
class Foo {
    final int i = -1;
    void foo() {
        // will not compile
        // IntSupplier is = () -> i++;
    }
}
```

## Void Compatibility Rule
If the body of the lambda is a statement expression, the lambda can still be assigned to a Functional Interface that has a void return type, even if the statement expression has a return type.

```java
// Single Abstract Method in the Consumer interface is `void accept(T t)` 
// Boolean.valueOf(true) actually returns a Boolean object
// However the following compiles fine according to the above rule mentioned
Consumer<Integer> ic = i -> Boolean.valueOf(true); 

// This does not compile:
// Consumer<Integer> ic = i -> {return Boolean.valueOf(true);};

// This does not compile either:
// Consumer<Integer> ic = i -> true;

// This does not compile either, you get the idea..
// Consumer<Integer> ic = i -> 1 == 1;
```

## Recursion with Lambda Expressions

```java
import java.util.function.IntUnaryOperator;

class Factorial {

    IntUnaryOperator fact;

    Factorial() {
        fact = i -> {
            if (i == 0) {
                return 1;
            } else {
                return i * fact.applyAsInt(i - 1);
            }
        };
    }

    public static void main(String[] args) {
        final Factorial factorial = new Factorial();
        System.out.println(factorial.fact.applyAsInt(5)); // 120
    }
}
```

## References
- [Oracle by Example](https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/Lambda-QuickStart/index.html)
- [The Java™ Tutorials - Lambda Expressions](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html)
- [Maurice Naftalin's Lambda FAQ](http://www.lambdafaq.org/)