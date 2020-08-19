---
layout: default
title:  "Spring Developer Exam"
---

# Spring Developer Exam
{:.no_toc}

* TOC
{:toc}

## Chapter 2
- `classpath` is a common prefix used in Spring. Paths starting with `classpath` are relative to `src/main/resources`.

#### Configuration classes
`@Configuration` is mostly used for classes to define beans. Such classes can also be further configured by adding extra annotations such as `@Profile` or `PropertySource`. Any Spring application has at its core one or more configuration classes. These classes either have bean declarations, or are configured to tell Spring where to look for bean declarations.

Here is a sample `@Configuration` annotated class:

```java
@Configuration
@ComponentScan(basePackages = {"biz.tugay"})
@PropertySource(value = "classpath:application.properties")
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

`@PropertySource` annotation can be used to tell Spring where to look for property values, which can be injected using placeholders `${}`. Here is a full example:

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

Spring provides a way to access the environment in which the current application is using with a bean of type `org.springframework.core.env.Environment`. This bean models two key aspects of an application environment: __properties__ and __profiles__. 