---
layout: default
title:  "Spring Boot Reference Documentation"
---

# Spring Boot Reference Documentation
{:.no_toc}

* TOC
{:toc}

## Overview
My notes from the [Spring Boot Reference Documentation](https://docs.spring.io/spring-boot/docs/current/reference/html/index.html).

## Getting Started
Sample application demonstrating a simple REST resource, a web layer test, and maven dependency to build an uber-jar.

Directory Layout

```
.
├── pom.xml
└── src
    ├── main
    │   └── java
    │       └── biz
    │           └── tugay
    │               └── DemoApplication.java
    │   
    └── test
        └── java
            └── biz
                └── tugay
                    └── DemoApplicationTest.java
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
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>
        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

DemoApplication.java

```java
package biz.tugay;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseStatus;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@RestController
@RequestMapping(value = "/demo", produces = MediaType.APPLICATION_JSON_VALUE)
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

    static class DemoDto {
        public String msg;
    }

    @GetMapping
    @ResponseStatus(HttpStatus.ACCEPTED)
    public DemoDto greet() {
        DemoDto demoDto = new DemoDto();
        demoDto.msg = "Hello World";
        return demoDto;
    }
}
```

DemoApplicationTest.java

```java
package biz.tugay;

import biz.tugay.DemoApplication.DemoDto;
import com.google.gson.Gson;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.MvcResult;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;

import static org.assertj.core.api.Assertions.assertThat;
import static org.springframework.web.servlet.MockMvcResultMatchers.status;

@RunWith(SpringRunner.class)
@WebMvcTest(DemoApplication.class)
public class DemoApplicationTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    public void mustGreet() throws Exception {
        MvcResult mvcResult = 
            mockMvc.perform(MockMvcRequestBuilders.get("/demo"))
                   .andExpect(status().isAccepted())
                   .andReturn();

        String responseBody = mvcResult.getResponse().getContentAsString();
        DemoDto demoDto = new Gson().fromJson(responseBody, DemoDto.class);
        assertThat(demoDto.msg).isEqualTo("Hello World");
    }
}
```

### Notes
- The `@RestController` annotation is known as a _stereotype_ annotation. It provides hints for people reading the code and for Spring that class plays a specific role. 
- To gracefully exit the application, press `ctrl + c`. `ctrl + z` will not work.
- `spring-boot-maven-plugin` handles the creation of the uber-jar.
- I did use `Gson` in the above example, but according to [this](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-json) the preferred library is Jackson, so another possibility here can be (without including Gson):

```java
import com.fasterxml.jackson.databind.ObjectMapper;

@Autowired
private ObjectMapper objectMapper;

DemoDto demoDto = objectMapper.readValue(responseBody, DemoDto.class);
```