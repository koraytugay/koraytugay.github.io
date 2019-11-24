---
layout: default
title:  "JAX-RS"
---

# JAX-RS
{:.no_toc}

* TOC
{:toc}

## Overview
[JAX-RS](https://en.wikipedia.org/wiki/Java_API_for_RESTful_Web_Services) is an API for creating RESTful Java applications and [Jersey](https://eclipse-ee4j.github.io/jersey/) is the reference implementation, which I also happen to use. I also have some [sample code in GitHub](https://github.com/koraytugay/pg-jaxrs) to easily play and experiment with JAX-RS. Happy learning!

## Resources
Resources are POJOs annotated with the [Path](https://jax-rs.github.io/apidocs/2.1/) annotation. Here is a resource that does nothing:

```java
@Path("resources")
public class MyResource {
}
```

### Paths and PathParams
Paths can have variables embed in the URIs, which makes them powerful. Following is an example where variable `name` is retrieved from the path:

```java
@Path("resources")
public class MyResource {
    @GET
    @Path("greet/{name}")
    @Produces(MediaType.TEXT_PLAIN)
    public String hello(@PathParam("name") String name) {
        return "Hello " + name;
    }
}
```

- Path parameters can be narrowed down to match reguler expressions, for example: `@Path("greet/{username: [a-zA-Z]*}")`
- A `@Path` value may or may not begin or end with a `/`, it makes no difference.

### HTTP Methods
Methods in resource classes are annotated with `GET`, `POST`, `DELETE` and so on for finding the matching method to accept the request. The example above is an example of a `GET` endpoint.

### Producting and Consuming MediaTypes
Both annotations, `Produces` and `Consumes` can be applied at class and method levels, and used with a [MediaType](https://jax-rs.github.io/apidocs/2.0/javax/ws/rs/core/MediaType.html). These annotations can be used to narrow down the matching of methods and transforing DTOs to appropriate types by an appropriate [MessageBodyWriter](https://javaee.github.io/javaee-spec/javadocs/javax/ws/rs/ext/MessageBodyWriter.html) implementation or a [MessageBodyReader](https://jax-rs.github.io/apidocs/2.0/javax/ws/rs/ext/MessageBodyReader.html) implementation.

#### Working with XML
The following example works with XML out of the box with Jersey, without the need of any additional libraries. 

Considering the following dependencies:

```xml
<dependencies>
  <dependency>
    <groupId>jakarta.ws.rs</groupId>
    <artifactId>jakarta.ws.rs-api</artifactId>
  </dependency>
  <dependency>
    <groupId>org.glassfish.jersey.core</groupId>
    <artifactId>jersey-server</artifactId>
    <scope>runtime</scope>
  </dependency>
  <dependency>
    <groupId>org.glassfish.jersey.containers</groupId>
    <artifactId>jersey-container-servlet</artifactId>
    <scope>runtime</scope>
  </dependency>
  <dependency>
    <groupId>org.glassfish.jersey.inject</groupId>
    <artifactId>jersey-hk2</artifactId>
    <scope>runtime</scope>
  </dependency>
</dependencies>
```

A very simple `Employee` modal:

```java
import javax.xml.bind.annotation.XmlRootElement;

import lombok.Getter;
import lombok.Setter;

@XmlRootElement
@Getter
@Setter
public class Employee
{
  String name;
}
```

and an `EmployeeResource`:

```java
import javax.ws.rs.*;
import javax.ws.rs.core.MediaType;

import biz.tugay.pg.jaxrs.modal.Employee;

@Path("employee")
public class EmployeeResource
{
  private static Employee onlyEmployee;

  @POST
  @Consumes(MediaType.APPLICATION_XML)
  public void addEmployee(Employee employee) {
    onlyEmployee = employee;
  }

  @GET
  @Produces(MediaType.APPLICATION_XML)
  public Employee getEmployee() {
    return onlyEmployee;
  }
}
```

This works out of the box, since it seems like `jersey-server` has a dependency to `jersey-media-jaxb` which provides `MessageBodyReader`s and `MessageBodyWriter`s for XML that implements JAXB. When I exclude this dependency:

```xml
<dependency>
  <groupId>org.glassfish.jersey.core</groupId>
  <artifactId>jersey-server</artifactId>
  <version>2.29.1</version>
  <scope>runtime</scope>
  <exclusions>
    <exclusion>
      <groupId>org.glassfish.jersey.media</groupId>
      <artifactId>jersey-media-jaxb</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

I started getting the following error:

```plaintext
MessageBodyWriter not found for media type=application/xml
``` 

#### Working with JSON
For whatever reason, Jersey seems to not include any writers that would work with JSON out of the box. However, once the following dependency is included:

```xml
<dependency>
  <groupId>org.glassfish.jersey.media</groupId>
  <artifactId>jersey-media-json-jackson</artifactId>
  <version>2.29.1</version>
  <scope>runtime</scope>
</dependency>
```

the very same example starts working with JSON format by changing the MediaType to `(MediaType.APPLICATION_JSON)`. At this point the `XmlRootElement` annotation is also not needed anymore.

#### A Note On MediaTypes
There is a lot more going on with MediaTypes, such as being able to consume or produce different MediaTypes from a single method, based on the preference of the client. For further details please see the [official documentation](https://eclipse-ee4j.github.io/jersey.github.io/documentation/latest/jaxrs-resources.html#d0e2167).

Continue from: https://eclipse-ee4j.github.io/jersey.github.io/documentation/latest/jaxrs-resources.html

### Query Parameters
[QueryParam](https://javaee.github.io/javaee-spec/javadocs/javax/ws/rs/QueryParam.html) annotation can be used to capture, well the query parameters from the URI. 

Here is an example:

```java
package biz.tugay.pg.jaxrs.resource;

import javax.ws.rs.*;
import javax.ws.rs.core.MediaType;

@Path("query")
public class QueryResource
{
  private static final String JOHN_DOE = "John Doe";

  @GET
  @Produces(MediaType.TEXT_PLAIN)
  public String greet(@QueryParam("name") @DefaultValue(JOHN_DOE) String name) {
    return greetPerson(name.isEmpty() ? JOHN_DOE : name);
  }

  private String greetPerson(String name) {
    return String.format("Greetings %s\n", name);
  }
}

```

and a few runs:

```bash
kt$ curl -i http://localhost:8080/api/query?name=Koray
# HTTP/1.1 200 OK
# Date: Sun, 24 Nov 2019 02:52:26 GMT
# Content-Type: text/plain
# Content-Length: 16
# Server: Jetty(9.4.22.v20191022)
# 
# Greetings Koray

kt$ curl -i http://localhost:8080/api/query?name=
# HTTP/1.1 200 OK
# Date: Sun, 24 Nov 2019 02:52:50 GMT
# Content-Type: text/plain
# Content-Length: 19
# Server: Jetty(9.4.22.v20191022)

# Greetings John Doe

kt$ curl -i http://localhost:8080/api/query
# HTTP/1.1 200 OK
# Date: Sun, 24 Nov 2019 02:52:57 GMT
# Content-Type: text/plain
# Content-Length: 19
# Server: Jetty(9.4.22.v20191022)
# 
# Greetings John Doe
```

The [official Jersey documentation](https://eclipse-ee4j.github.io/jersey.github.io/documentation/latest/jaxrs-resources.html#d0e2271) has very clear and detailed information on the rest of the lesser used possibitiles such as `MatrixParam`, `HeadParam`, `CookieParam` and `FormParam`.

## Random Notes
- [Answer](https://stackoverflow.com/a/36033943) on Stackoverflow to question: _What is the relationship between Jersey, JAXB, JAX-RS, Moxy, Jackson, EclipseLink Moxy, json and xml? [closed]?_