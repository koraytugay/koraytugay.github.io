---
layout: default
title:  "Spring Boot Recipies"
---

# Spring Boot Recipies
{:.no_toc}

* TOC
{:toc}

## Chapter 1 - Hello World
A Hello World in Spring Boot can be done as follows. Directory Layout:

```
.
├── pom.xml
└── src
    └── main
        └── java
            └── biz
                └── tugay
                    └── DemoApplication.java
```

pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>
    <groupId>biz.tugay</groupId>
    <artifactId>my-spring-boot-starter</artifactId>
    <version>1.0-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.0.RELEASE</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
    </dependencies>
</project>
```

DemoApplication.java

```java
package biz.tugay;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;

@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        ConfigurableApplicationContext ctx = 
            SpringApplication.run(DemoApplication.class, args);
        System.out.println(ctx.getBeanDefinitionCount());
    }
}
```

Note that when `spring-boot-starter` was added to dependencies, no version information was added. This is because `spring-boot-starter-parent` has default for core dependencies needed in a Spring Boot application, such as the Spring Framework, Logback for logging and Spring Boot itself.

`@SpringBootApplication` annotation is a combination of the following annotations:

- `@Configuration`
- `@ComponentScan`
- `@EnableAutoConfiguration`

## Chapter 2 - Spring Boot Basics
### Configuring a Bean using `@Component`
Any class annotated with the `@Component` annotation will be picked up by and instantiated by Spring Boot.

If we add the following class to our project, we do not need to change anything else, the message will be printed:

```java
package biz.tugay;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

import javax.annotation.PostConstruct;

@Component
public class MyFirstBean {

    @Value("${biz.tugay.myCustomProperty}")
    private String message;

    @PostConstruct
    public void printMessage() {
        System.out.println(message);
    }
}
```

Here, I also added `application.properties` file in `resources` folder:

```properties
biz.tugay.myCustomProperty=Hello World!
```

### Configuring a Bean using `@Bean` on a Factory Method
`@Bean` annotation on methods can be used to create beans from factory methods. Given again we have the same `application.properties` file from above, without using the `@Component` annotation, create a class as follows:

```java
package biz.tugay;

import org.springframework.beans.factory.annotation.Value;

public class MySecondBean {

    private String message;

    public void printMessage() {
        System.out.println(message);
    }

    @Value("${biz.tugay.myCustomProperty}")
    public void setMessage(String message) {
        this.message = message;
    }
}
```

And in our main class, we can create a bean from this class using a factory method:

```java
package biz.tugay;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        ConfigurableApplicationContext ctx = 
            SpringApplication.run(DemoApplication.class, args);
        MySecondBean mySecondBean = ctx.getBean(MySecondBean.class);
        mySecondBean.printMessage();
    }

    @Bean
    public MySecondBean mySecondBean() {
        return new MySecondBean();
    }
}
```

This gives more power over bean creation, and also allows us to create beans from 3<sup>rd</sup> party libraries. For example, we could have a `RestTemplate` bean configured in a specific way using this approach, if we wanted to.

### Externalize Properties
We have already used the `application.properties` file above in our examples. The following is the hierarchy where the properties are read from:

1. Command line arguments
1. `application-{profile}.properties` outside the packaged application
1. `application.properties` outside the packaged application
1. `application-{profile}.properties` in the packaged application
1. `application.properties` in the packaged application

Simply having an `application.properties` where you have the `jar` file will override the `application.properties` bundled within the application, for example.

To make use of a specific profile, add `--spring.profile.active=` in command line. To override any arbirary propery in command line arguments, pass the desired value with `--propertyName=propertyValue`.

### Loading Properties from Different Configuration File
Properties from arbitrary files can be loaded by loading them using another annotation: `@PropertySource`. Here is an example

```java
@PropertySource("classpath:your-external.properties")
```