---
layout: default
title:  "Spring Boot Recipies"
---

# Spring Boot Recipies
{:.no_toc}

* TOC
{:toc}

## Chapter 1
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