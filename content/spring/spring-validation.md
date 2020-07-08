---
layout: default
title:  "Spring Validation"
---

# Spring Validation
{:.no_toc}

* TOC
{:toc}

## Hello World
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

ValidationController.java

```java
package biz.tugay;

import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.validation.Valid;
import javax.validation.constraints.Size;

@RestController
@RequestMapping(value = "/validation", 
    produces = MediaType.APPLICATION_JSON_VALUE)
public class ValidationController {

    static class RequestDto {

        @Size(min = 2, max = 6)
        public String name;
        
        // getters and setters are required
        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }
    }

    @GetMapping
    public void helloWorld(@Valid RequestDto requestDto) {
    }
}
```

This is actually everything needed. Here is a sample execution:

```bash
kt$ (curl localhost:8080/validation?name=koraytugay&age=42) | jq .
# {
#   "timestamp": "2020-07-07T23:45:29.403+0000",
#   "status": 400,
#   "error": "Bad Request",
#   "errors": [
#     {
#       "codes": [
#         "Size.requestDto.name",
#         "Size.name",
#         "Size.java.lang.String",
#         "Size"
#       ],
#       "arguments": [
#         {
#           "codes": [
#             "requestDto.name",
#             "name"
#           ],
#           "arguments": null,
#           "defaultMessage": "name",
#           "code": "name"
#         },
#         6,
#         2
#       ],
#       "defaultMessage": "size must be between 2 and 6",
#       "objectName": "requestDto",
#       "field": "name",
#       "rejectedValue": "koraytugay",
#       "bindingFailure": false,
#       "code": "Size"
#     }
#   ],
#   "message": "Validation failed for object='requestDto'. Error count: 1",
#   "path": "/validation"
# }
```

### Customising the Message using `BindingResult`

```java
@ResponseStatus(HttpStatus.BAD_REQUEST)
static class BadRequestException extends RuntimeException {
    public BadRequestException(String message) {
        super(message);
    }
}

@GetMapping
public void helloWorld(@Valid RequestDto requestDto, 
                       BindingResult bindingResult) {
    if (bindingResult.hasErrors()) {
        List<FieldError> fieldErrors = bindingResult.getFieldErrors();
        for (FieldError error : fieldErrors) {
            throw new BadRequestException(error.getRejectedValue() 
                + " not valid value for:" + error.getField());
        }
    }
}
```

Sample response:

```json
{
  "timestamp": "2020-07-07T23:54:25.748+0000",
  "status": 400,
  "error": "Bad Request",
  "message": "koraytttt not valid value forname",
  "path": "/validation"
}
```

We could also simply map all `ConstraintViolationException` to return a `400` instead of a `500`. In order to learn how to do that, check out [this](https://reflectoring.io/bean-validation-with-spring-boot/#validating-path-variables-and-request-parameters) article.

## Notes
This example implicitly shows how arbitrary number of parameters can also be serialzed into a data transfer object. Note how this is a `GET` request with a query parameter, but we are treating it as a data transfer object. 

## References
- [Validation Annotations](https://docs.jboss.org/hibernate/beanvalidation/spec/2.0/api/javax/validation/constraints/package-summary.html)
- [All You Need To Know About Bean Validation With Spring Boot](https://reflectoring.io/bean-validation-with-spring-boot/)