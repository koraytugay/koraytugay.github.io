---
layout: default
title:  "Spring Developer Exam"
---

# Spring Developer Exam
{:.no_toc}

* TOC
{:toc}

## Chapter 2

### Configuration classes
`@Configuration` is mostly used for classes to define beans. Such classes can also be further configured by adding extra annotations such as `@Profile` or `@PropertySource`. Any Spring application has at its core one or more configuration classes. These classes either have bean declarations, or are configured to tell Spring where to look for bean declarations.

Here is a sample `@Configuration` annotated class:

```java
@Configuration
class MyConfiguration {
}
```

Configuration classes can have bean definitions inside them. A bean declaration inside a configuration class is any method annotated with `@Bean` that returns an instance. 

```java
@Configuration
class MyConfiguration {

    @Bean
    MyBean myBean() {
        return new MyBean();
    }
}
```

Every bean in a Spring application is a singleton by default, unless explicitly configured otherwise. 

Bean declarations that are not a part of the configuration classes are identified using a process named "component scanning" which can be enabled using the `@ComponentScan` annotation. 

`@PropertySource` annotation can be used to tell Spring where to look for property values, which can be injected using placeholders `${}`. Here is a full example which also includes `@ComponentScan`:

```java
@Configuration
@ComponentScan(basePackages = {"biz.tugay"})
@PropertySource(value = "classpath:application.properties")
class MyConfiguration {
}

@Component
class MyComponent {

    String foo;

    MyComponent(@Value("${foo}") String foo) {
        this.foo = foo;
    }

    String getFoo() {
        return foo;
    }
}
```

Note that `classpath` is a common prefix used in Spring. Paths starting with `classpath` are relative to `src/main/resources`. Other possibilites are using `file:` for an absoulte location or `http:` for a resource, which will be of type `UrlResource`.

#### Using Multiple Configuration Classes
Multiple configuration classes can be used to create a Spring Application Context while running tests by using `@ContextConfiguration`. Here is an example:

```java
public class Foo {
}

public class Bar {

    int val;

    public Bar(int val) {
        this.val = val;
    }
}

@Configuration
public class ConfigurationOne {

    @Bean
    public Foo foo() {
        return new Foo();
    }

    @Bean
    public Bar bar() {
        return new Bar(42);
    }
}

@Configuration
public class ConfigurationTwo {

    @Bean
    public Bar bar() {
        return new Bar(-1);
    }
}


@RunWith(SpringRunner.class)
@ContextConfiguration(classes = {
        ConfigurationOne.class,
        ConfigurationTwo.class
})
public class MyTest {

    @Autowired
    Foo foo;

    @Autowired
    Bar bar;

    @Test
    public void testBeans() {
        Assert.assertNotNull(foo);
        Assert.assertNotNull(bar);
        System.out.println(bar.val);
    }
}
```

The above example required me to add the following dependencies:

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.2.8.RELEASE</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.13</version>
    <scope>test</scope>
</dependency>
```

### Environment
Spring provides a way to access the environment in which the current application is using with a bean of type `org.springframework.core.env.Environment`. This bean models two key aspects of an application environment: __properties__ and __profiles__. 

### Bean Injection
`@Autowired` is the annotation to use inject beans to other beans. When there is only a single type of bean, injection is straightforward. When there are multiple types of a certain interface or multiple `@Bean` declarations of a certain class, things get interesting. This is one way how bean injection can work based on bean name:

```java
@Configuration
@ComponentScan
public class Main {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext applicationContext
                = new AnnotationConfigApplicationContext(Main.class);
        MyOtherBean myOtherBean = applicationContext.getBean(MyOtherBean.class);
        System.out.println(myOtherBean.myFirstBean.value);
    }

    @Bean
    public MyBean myFirstBean() {
        return new MyBean("myFirstBean");
    }

    @Bean
    public MyBean mySecondBean() {
        return new MyBean("mySecondBean");
    }
}

class MyBean {

    String value;

    MyBean(String value) {
        this.value = value;
    }
}

@Component
class MyOtherBean {

    MyBean myFirstBean;

    @Autowired
    MyOtherBean(MyBean myFirstBean) {
        this.myFirstBean = myFirstBean;
    }
}
```

This prints `myFirstBean`. Spring gives the name `myFirstBean` to the first bean created, and name `mySecondBean` to the second one created. The argument name in `MyOtherBean` constructor `myFirstBean` is enough for Spring to decide which of these to inject. Another option can be to use the `@Qualifier` annotation:

```java
@Component
class MyOtherBean {

    MyBean myFirstBean;

    @Autowired
    MyOtherBean(@Qualifier("mySecondBean") MyBean myBean) {
        this.myFirstBean = myBean;
    }
}
```

Giving names to beans declared by using the `@Component` annotation would be as follows:

```java
@Component("myFirstBean")
class MyBean {}
```

Since multiple beans with different names can not be declared this way, a common Interface must be extracted, and the Interface must be used in injection.

And customising names using `@Bean` is possible, and also multiple names can be given:

```java
@Bean(name = {"fooBean", "barBean"})
public MyBean myFirstBean() {
    return new MyBean("myFirstBean");
}
```

#### @Autowire on methods
`@Autowire` works on any method as long as Spring can figure out what bean to autowire. Given a bean of type `MyBean` exists, it will be injected in the snippet below:

```java
@Component
public class Foo {
    @Autowired
    public void gibberishMethodName(MyBean myBean) {
        System.out.println("My Bean:" + myBean);
    }
}
```

This is useful for development of special Spring configuration classes that have methods that are called by Spring directly and have parameters that have to be configured.

### Value Injection
For injecting primitive values, the `@Value` annotation is used. 

```java
@Configuration
@ComponentScan
@PropertySource(value = "classpath:application.properties")
public class Main {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext applicationContext
                = new AnnotationConfigApplicationContext(Main.class);
        MyBean myBean = applicationContext.getBean(MyBean.class);
        System.out.println(myBean.isTrue);
    }
}

@Component
class MyBean {

    boolean isTrue;

    @Autowired
    MyBean(@Value("${isTrue}") boolean isTrue) {
        this.isTrue = isTrue;
    }
}
```

The value of the `isTrue` property in `application.properties` will be used.

### Bean Scopes
Default scope for beans in Spring is singleton. Unless otherwise declared, all beans are by default singletons. A singleton bean is created when the application is bootstrapped, and is managed by Spring until the application is shutdown.

### Profiles
A profile is a logical group of bean definitions that is registered within the Spring container when the profile is active. Profiles can also group property files or property values that are supposed to be active only when the profile is active.