---
layout: default
title:  "JAX-RS"
---

# JAX-RS
{:.no_toc}

* TOC
{:toc}

## Overview

## Resources
### Paths
Resources are POJOs annotated with the [Path](https://jax-rs.github.io/apidocs/2.1/) annotation. Resources have methods that handle requests at particular endpoints. Paths can have variables embed in the URIs, which makes them very useful and powerful.

```java
@Path("resources")
public class MyResource {
    @GET
    @Path("greet/{name}")
    public String hello(@PathParam("name") String name) {
        return "Hello " + name;
    }
}
```

- Resource class must be public.
- Path parameters can be narrowed down to match reguler expressions, for example: `@Path("greet/{username: [a-zA-Z]*}")`
- A `@Path` value may or may not begin or end with a `/`, it makes no difference.

### HTTP Methods
Methods in resource classes are annotated with `GET`, `POST`, `DELETE` and so on for finding the matching method to accept the request.

### Produces and Consumes
Continue from: https://eclipse-ee4j.github.io/jersey.github.io/documentation/latest/jaxrs-resources.html

## Random Notes
- [Answer](https://stackoverflow.com/a/36033943) on Stackoverflow to question: _What is the relationship between Jersey, JAXB, JAX-RS, Moxy, Jackson, EclipseLink Moxy, json and xml? [closed]?_
- Sample code to play with on [GitHub](https://github.com/koraytugay/pg-jaxrs)