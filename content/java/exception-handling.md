---
layout: default
title:  "Exception Handling"
---

# Exception Handling
{:.no_toc}

* TOC
{:toc}


## Handle and Declare Pattern
- Catching `Exception` and rethrowing is not really catching any `Exception` 

```java
void f() throws IOException {
    try {
        throw new IOException();
    } catch (Exception e) {  // Not really!
        throw e;             // Due to this guy here!
    }                        // Catching only types declared in method signature
}
```

- Note how it _looks like_ we are catching and re-throwing an `Exception`
  - In reality we are only catching and re-throwing an `IOException`
  - If we were actually catching and re-throwing an `Exception` the code would not have compiled since method is declaring only an `IOException`
  - Since the code compiles fine, we can assume that the compiler knows we mean we only want to catch the declared Exception type(s)  
- The following code does __not__ compile, where we in fact are throwing an `Exception`
  - This is not allowed, you can not declare a narrow type and throw a broader type
  - Remember `IOException` is narrow compared to `Exception`

```java
// does not compile
void f() throws IOException {
    throw new Exception();
}
```

__How This Feature Prevents Compilation Failures__

Imagine the following scenario:

```java
class A extends Exception {}
class B extends Exception {}

void foo() throws A, B {}

void bar() throws A, B {
    try {
        foo();
    } catch (A | B e) {
        throw e;
    }
}
``` 

- We did not use `catch (Exception e)`, instead we used `catch (A |B e)`
- Imagine foo gets _less brittle_ by not throwing `B` anymore
  - Signature becomes `void foo() throws A`
- This breaks the compilation because `bar` is now catching `B` which is never thrown in `foo`
  - Catching a checked type (exception of the big Exception class) is not allowed, if that particular type or a subclass is never thrown
  - Further reading [here]()
- By using `catch (Exception e)` where we still have `throws A, B` in method `bar`, we are only left with `throws B` which is never thrown in `bar`
  - That is not a compilation error cause, it is just an info

```java
void foo() throws A {}

void bar() throws A, B {     // B is never thrown, but it is legal to declare
    try {
        foo();
    } catch (Exception e) {
        throw e;
    }
}
```

## Exceptions in Inheriting Methods
- Inheriting method does not need to declare the Exception from parent if no Exception is actually thrown

```java
class MyException extends Exception {}

class A {
    void a() throws MyException {};
}

class B extends A {
    public void a() {}  // no need for throws MyException
}
```

- Exception must be handled based on the type

```java
A a = new B();
try {
    a.a();
} catch (MyException e) { // Must handle exception
    e.printStackTrace();  // Call is on type A not B
}
```

- Inheriting methods can not declare broader or new _checked_ Exceptions
  - However declaring _unchecked_ Exceptions is allowed

```java
class A {
    void f() {}
}

class B extends A {
    // this is fine
    // throwing a CheckedException would not compile
    void f() throws RuntimeException {}  
}
```  
