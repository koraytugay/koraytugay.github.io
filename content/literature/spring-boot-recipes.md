---
layout: default
title:  "Spring Boot Recipes"
---

# Spring Boot Recipes
{:.no_toc}

* TOC
{:toc}

## Overview
These are my notes from the book [Spring Boot 2 Recipes: A Problem-Solution Approach](https://www.amazon.ca/dp/1484239628). I highly recommend this book to anyone who is already somewhat familiar with the Spring Framework, but wants to get familiar with Spring Boot. Kudos to Marten Deinum on his great work. If you like what you see here in my notes, I highly encourage you to buy the book which has many more useful information and examples. 

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

### Integration Testing with Spring Boot
`@SpringBootTest` will make the test class load the context fully, and run the tests within the context. This is good for integration testing of beans. Unless a `@MockBean` is used in a class, the loaded context will be re-used. Here is an example to get you started:

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class MyBeanTest {

    @Autowired
    public MyBean myBean;

    @Test
    public void valMustBeExpectedValue() {
        Assertions.assertThat(myBean.val()).isEqualTo(-1);
    }
}
```

Check out [this](https://stackoverflow.com/questions/44200720) answer to refresh your knowledge on the difference between `@Mock` and `@MockBean`. When running integration tests and there needs a requirement to mock a bean, you most likely want the `@MockBean` annotation.

## Chapter 3 - Spring MVC
Simply adding the `spring-boot-starter-web` dependency does a lot of work and changes how the application starts. Following is a simple hello world web application:

Directory Layout
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
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
</project>
```

DemoApplication.java

```java
package biz.tugay;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

The default behaviour for `spring-boot-starter-web` is as follows:

1. Start an embedded Tomcat server on port 8080.
1. Register and enable default Servlet Filters.
1. Set up static resource handling for `.css`, `.js` and `favicon.ico` files.
1. Enable integration with WebJars (_What does this mean?_).
1. Setup basic error handling features.
1. Configure the `DispatcherServlet`

An example to create a Controller and return json is as follows:

```java
package biz.tugay;

import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    static class ResponseDto {
        public String message = "Hello World!";
    }

    @GetMapping(produces = MediaType.APPLICATION_JSON_VALUE)
    public ResponseDto hello() {
        return new ResponseDto();
    }
}
```

with a sample run:

```bash
curl -v localhost:8080

# * Trying ::1...
# * TCP_NODELAY set
# * Connected to localhost (::1) port 8080 (#0)
# > GET / HTTP/1.1
# > Host: localhost:8080
# > User-Agent: curl/7.64.1
# > Accept: */*
# > 
# < HTTP/1.1 200 
# < Content-Type: application/json;charset=UTF-8
# < Transfer-Encoding: chunked
# < Date: Sun, 14 Jun 2020 14:30:05 GMT
# < 
# * Connection #0 to host localhost left intact
# {"message":"Hello World!"}
# * Closing connection 0
```