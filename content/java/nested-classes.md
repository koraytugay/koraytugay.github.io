---
layout: default
title:  "Nested Classes"
---

# Nested Classes
{:.no_toc}

* TOC
{:toc}

## Static Nested Classes

```java
class A {
    static class B {
    }
}
A.B ab = new A.B();
```

- Static Nested Classes __can not__ access the outer class fields

```java
class A {
    int a;
    static class B {
        // This will not compile
        // int b = a;
    }
}
```

- Static Nested Classes can access the outer class private constructor or private static methods
  - Static Nested Classes can also access the outer classes private fields via its own instance of the outer class

```java
class F {
    private F() {}
    private int f;
    
    static class G {
        F f = new F();  // Can invoke private constructor
        int val = f.f;  // Can access private field via its own instance
    }
}
```

- Static Nested Classes can be private
  - In that case only the enclosing class can access the class or the fields

```java
class A {
    private static class B {
        public void a(){};
        private void b(){};
    }
    B getB() {
        B b = new B();
        b.a();
        b.b(); // accessing a private method of a static nested class
        // No one can do anything with this reference except A itself
        return b;
    }
}

// This is fine but useless
new A().getB();

// This will not compile
// A.B b = new A().getB();

// This will not compile either
// new A().getB().a();
```

## Inner Classes
- Can only live together with an instance of the enclosing class

```java
class A {
    class B {
    }
}
A.B ab = new A().new B();
```

- Inner Classes __can__ access the outer class fields (including private)

```java
class A {
    private int a;
    private void a() {}
    class B {
        int b = a;
        void b() { a(); }
    }
}
```

- Outer classes __can__ access the inner class field (including private)

```java
class A {
    class B {
        private int b;
    }
    int getB() {
        return new B().b;
    }
}
```

- Inner classes can __not__ declare static fields
  - Except final static fields

```java
class A {
    class B {
        // This is fine:
        static final int y = 10;
        // This will not compile:
        // static int x = 10;
    }
}
```

## Local Classes
- A class declared within a block of code

```java
class A {
    void a() {
        class B {}
    }
}
```
- In addition to all instance fields including private fields, Local Classes can access effectively final local variables

```java
class A {
    void a(int x) {
        int y = -1;
        class B {
            // This is fine
            int z = x + y;
        }
    }
}
```

## Anonymous Classes

```java
interface A {}
class B {
    A a = new A(){};
}
```

- Anonymous Classes can capture values from surroundings

```java
interface A {
    int a ();
}

class B {
    int b = 42;
    A a = new A() {
        @Override
        public int a() {
            return b;
        }
    };
}
```

- Above example can be written as

```java
interface A {
    int a ();
}

class B {
    int b = 42;
    A a = () -> b;
}
```

## Examples

```java
class Foo {
    class Bar {
    }

    static class Qux {
    }

    Bar fooBar() {
        Bar bar;
        bar = new Bar();
        bar = this.new Bar();
        return bar;
    }
}

class Baz {
    void baz() {
        Foo.Bar foobar = new Foo().new Bar();
        Foo.Qux qux = new Foo.Qux();
    }
}
```
