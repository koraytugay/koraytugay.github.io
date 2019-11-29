---
layout: default
title:  "JAX-RS"
---

# JAX-RS
{:.no_toc}

* TOC
{:toc}

## Overview
[JAX-RS](https://projects.eclipse.org/projects/ee4j.jaxrs), _Jakarta RESTful Web Services_, is an API for creating RESTful Java applications and [Jersey](https://eclipse-ee4j.github.io/jersey/) is the reference implementation, which I also happen to use. I also have some [sample code in GitHub](https://github.com/koraytugay/pg-jaxrs) to easily play and experiment with JAX-RS. Happy learning, and do not forget to check the [API docs](https://jax-rs.github.io/apidocs/2.1/)!

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

### Producing and Consuming MediaTypes
Both annotations, [Produces](https://jax-rs.github.io/apidocs/2.1/javax/ws/rs/Produces.html) and [Consumes](https://jax-rs.github.io/apidocs/2.1/javax/ws/rs/Consumes.html) can be applied at class and method levels, and used with a [MediaType](https://jax-rs.github.io/apidocs/2.1/javax/ws/rs/core/MediaType.html). These annotations can be used to narrow down the matching of methods and transforing DTOs to appropriate types by an appropriate [MessageBodyWriter](https://javaee.github.io/javaee-spec/javadocs/javax/ws/rs/ext/MessageBodyWriter.html) implementation or a [MessageBodyReader](https://jax-rs.github.io/apidocs/2.1/javax/ws/rs/ext/MessageBodyReader.html) implementation.

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
@XmlRootElement
@Getter
@Setter
public class Employee {
    String name;
}
```

and an `EmployeeResource`:

```java
@Path("employee")
public class EmployeeResource {
    
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

### Query Parameters
[QueryParam](https://javaee.github.io/javaee-spec/javadocs/javax/ws/rs/QueryParam.html) annotation can be used to capture, well the query parameters from the URI. Here is an example:

```java
@Path("query")
public class QueryResource {
    
    private static final String JOHN_DOE = "John Doe";
  
    @GET
    @Produces(MediaType.TEXT_PLAIN)
    public String greet(@QueryParam("name") @DefaultValue(JOHN_DOE) String s) {
      return greetPerson(s.isEmpty() ? JOHN_DOE : s);
    }
  
    private String greetPerson(String s) {
      return String.format("Greetings %s\n", s);
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

The [official Jersey documentation](https://eclipse-ee4j.github.io/jersey.github.io/documentation/latest/jaxrs-resources.html#d0e2271) has very clear and detailed information on the rest of the lesser used possibitiles such as `MatrixParam`, `HeadParam`, `CookieParam`, `FormParam`, and my personal favorite [BeanParam](https://jax-rs.github.io/apidocs/2.1/javax/ws/rs/BeanParam.html).

### Sub-Resources
[Sub-resources](https://eclipse-ee4j.github.io/jersey.github.io/documentation/latest/jaxrs-resources.html#d0e2542) can be used to further organize the design, if there are several method-level resources in a resource class that all are a part of a particular path. 

```java
@Path("myResource")
public class MyResource {
    @Path("sub")
    public MySubResource greet() {
      return new MySubResource();
    }
}

public class MySubResource {
    @GET
    @Produces(MediaType.TEXT_PLAIN)
    public String hello() {
      return "Hello from SubResource!\n";
    }
}
```

And here is a sample invocation:

```bash
kt$ curl -i http://localhost:8080/api/myResource/sub
# HTTP/1.1 200 OK
# Date: Sun, 24 Nov 2019 03:11:22 GMT
# Content-Type: text/plain
# Content-Length: 24
# Server: Jetty(9.4.22.v20191022)
# 
# Hello from SubResource!
```

## Representations and Responses
So far we have been returning `200 OK` in our responses, but we can do better. Following is an example that returns a `201 Created`:

```java
@Path("employee")
public class EmployeeResource {

    static List<Employee> employees = new ArrayList<>();

    @POST
    @Produces(MediaType.APPLICATION_JSON)
    @Path("{name}")
    public Response createEmployee(@PathParam("name") String name) {
        Employee e = new Employee(employees.size() + 1, name);
        employees.add(e);
        return Response.status(Response.Status.CREATED).entity(e).build();
    }

}
```

And an example invocation:

```bash
kt$ curl -i -X POST http://localhost:8080/api/employee/koray
# HTTP/1.1 201 Created
# Date: Sun, 24 Nov 2019 23:18:05 GMT
# Content-Type: application/json
# Content-Length: 23
# Server: Jetty(9.4.22.v20191022)
# 
# {"id":1,"name":"koray"}
```

Response building provides other functionality such as adding header values in responses, setting the entity tag and last modified date of the representation.

### Response Builder Shortcuts and the Location Header
The [Response](https://jax-rs.github.io/apidocs/2.1/javax/ws/rs/core/Response.html) class has some shortcuts, here is an example:

```java
@Path("employee")
public class EmployeeResource {

    static List<Employee> employees = new ArrayList<>();

    @POST
    @Path("{name}")
    public Response createEmployee(@PathParam("name") String name,
                                   @Context UriInfo uriInfo) {
        Employee e = new Employee(employees.size() + 1, name);
        employees.add(e);
        UriBuilder path = uriInfo
                .getBaseUriBuilder()
                .path(EmployeeResource.class)
                .path(valueOf(e.getId()));
        return Response.created(path.build()).build();
    }

    @GET
    @Path("{id}")
    @Produces(MediaType.APPLICATION_JSON)
    // We could have returned the Employee directly instead of a Response
    // The default code is 200 already
    public Response employee(@PathParam("id") int id) {
        return Response
                .ok(employees.get(id - 1))
                .build();
    }

}
```

with a sample run:

```bash
kt$ curl -i -X POST  http://localhost:8080/api/employee/koray
# HTTP/1.1 201 Created
# Date: Mon, 25 Nov 2019 02:21:45 GMT
# Location: http://localhost:8080/api/employee/1
# Content-Length: 0
# Server: Jetty(9.4.22.v20191022)

kt$ curl -i http://localhost:8080/api/employee/1
# HTTP/1.1 200 OK
# Date: Mon, 25 Nov 2019 02:21:54 GMT
# Content-Type: application/json
# Content-Length: 23
# Server: Jetty(9.4.22.v20191022)

# {"id":1,"name":"koray"}
```

Things to note in the example above:
- We made use of the [Context](https://jax-rs.github.io/apidocs/2.1/javax/ws/rs/core/Context.html) annotation, and retrieved the absolute path of the resource itself.
- We made use of the [created](https://jax-rs.github.io/apidocs/2.1/javax/ws/rs/core/Response.html#created-java.net.URI-) method from `Response` class, passing in the path the resource that can be retrieved from. 
  - This ended up in including the `Location` header in the response automatically.

Make sure to check other shortcuts such as: [accepted](https://jax-rs.github.io/apidocs/2.1/javax/ws/rs/core/Response.html#accepted) and [noContent](https://jax-rs.github.io/apidocs/2.1/javax/ws/rs/core/Response.html#noContent).

## JAX-RS in Client Side
JAX-RS also provides an API for the client side, i.e. for making requests. The following example demonstrates several features of JAX-RS in client side including:

- Building `WebTarget`s by chaining `path` calls.
- Sending a `POST` request with an entity in the body.
- Sending a `GET` request and declaring the accepted `MediaType`.
- Handling generic types while reading responses.
- Dynamically populating parameterized URIs by `resolveTemplate`.

```java
public class RestApiClient
{
  final WebTarget BASE_TARGET = ClientBuilder.newClient().target("http://localhost:8080/api");

  private  WebTarget MESSAGES = BASE_TARGET.path("messages");

  final WebTarget BY_ID = MESSAGES.path("{id}");

  public static void main(String[] args) {
    Message message = new Message();
    message.setAuthor("koraytugay");
    message.setContent("From JAX-RS client!");

    Response response = MESSAGES.request().post(Entity.json(message));
    System.out.println(response);
    // {method=POST, uri=http://localhost:8080/api/messages, status=201, reason=Created}

    response = MESSAGES.request(MediaType.APPLICATION_JSON).get();
    System.out.println(response);
    // {method=GET, uri=http://localhost:8080/api/messages, status=200, reason=OK}

    // Handling Generic Types, such as List<Message>
    List<Message> messages = response.readEntity(new GenericType<List<Message>>(){});
    for (Message m: messages) {
      System.out.println(m);
    }

    message = BY_ID.resolveTemplate("id", 1).request(MediaType.APPLICATION_JSON).get(Message.class);
    System.out.println(message);
    // Message(id=1, author=Koray Tugay, content=Hello World!, created=Mon Nov 25 20:33:38 EST 2019)
  }
}
```

### Invocations
JAX-RS also has a concept that is called invocations: where you prepare an object that is ready to make the request, that includes all the requiered information, which can be invoked at any time.

Here is an example:
```java
public Invocation getMessageInvocation(int id) {
  Invocation byId = BY_ID.resolveTemplate("id", 1)
                         .request(MediaType.APPLICATION_JSON)
                         .buildGet();
  return byId;  // Call anytime with byId.invoke();
}
```

## References
- [Jersey Official Documentation](https://eclipse-ee4j.github.io/jersey.github.io/documentation/latest/index.html)
- [Developing RESTful APIs with JAX-RS](https://www.youtube.com/playlist?list=PLqq-6Pq4lTTZh5U8RbdXq0WaYvZBz2rbn)
- [Answer](https://stackoverflow.com/a/36033943) on Stackoverflow to question: _What is the relationship between Jersey, JAXB, JAX-RS, Moxy, Jackson, EclipseLink Moxy, json and xml?_