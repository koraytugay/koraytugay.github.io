---
layout: default
title:  "Design Patterns"
---

# Design Patterns
{:.no_toc}

* TOC
{:toc}

## DataSource - Model - Repository Pattern
__Data__

Most likely a database but may be a flat file as follows in `employees.xml` 
```xml
<employees>
    <employee>
        <id>1</id>
        <name>Koray</name>
    </employee>
    <employee>
        <id>2</id>
        <name>Deniz</name>
    </employee>
    <employee>
        <id>3</id>
        <name>Toprak</name>    
    </employee>
</employees>
```
__Model__
Represents a single entity in memory
```java
class Employee {
    int id;
    String name;
}
```

__DataSource__
Responsible for reading from / writing to underlying physical data.
```java
class DataSource {
    Map<String, String> configuration; // username, password etc..
    String path; // filepath, jdbcurl etc..
    
    InputStream query(String query); // SELECT FROM etc..
}
```

__Repository__
> "A Repository represents all objects of a certain type as a conceptual set. It acts like a collection, except with more elaborate querying capability."
>
> Eric Evans, Domain Driven Design
```java
class EmployeeRepository {
   DataSource ds;
   
   Employee byId(int id);
   Employee[] all(); 
}
```