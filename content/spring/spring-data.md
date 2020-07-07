---
layout: default
title:  "Spring Data"
---

# Spring Data
{:.no_toc}

* TOC
{:toc}

## Overview
I am running `MySQL Ver 14.14 Distrib 5.6.47` in my local with [sakila](https://dev.mysql.com/doc/sakila/en/) database loaded. Related documentation can be found [here](https://docs.spring.io/spring/docs/5.2.7.RELEASE/spring-framework-reference/data-access.html#jdbc). 

## JdbcTemplate Hello World
The API docs for `JdbcTemplate` can be found [here](https://docs.spring.io/spring-framework/docs/5.2.7.RELEASE/javadoc-api/org/springframework/jdbc/core/JdbcTemplate.html).

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
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
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

application.properties

```properties
spring.jpa.hibernate.ddl-auto=none
spring.datasource.url=jdbc:mysql://localhost:3306/sakila?useUnicode=true&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=
```

ActorDao.java

```java
@Component
public class ActorDao {

    static class Actor {
        String first_name;
        String last_name;
    }

    static class ActorRowMapper implements RowMapper<Actor> {
        @Override
        public Actor mapRow(ResultSet resultSet, int rowNumber) throws SQLException {
            Actor actor = new Actor();
            actor.first_name = resultSet.getString("first_name");
            actor.last_name = resultSet.getString("last_name");
            return actor;
        }
    }

    private final JdbcTemplate jdbcTemplate;

    private final ActorRowMapper actorRowMapper = new ActorRowMapper();

    public ActorDao(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    public void printActors() {
        List<Actor> actors = 
          jdbcTemplate.query("SELECT * FROM Actor LIMIT 10", actorRowMapper);
        actors.forEach(actor -> 
          System.out.println(
            String.format("%s %s", actor.first_name, actor.last_name)));
    }
}
```

DemoApplication.java

```java
@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        ConfigurableApplicationContext ctx = 
            SpringApplication.run(DemoApplication.class, args);

        ActorDao actorDao = ctx.getBean(ActorDao.class);
        actorDao.printActors();
    }
}
```

### NamedParameterJdbcTemplate
A simple example with `NamedParameterJdbcTemplate`:

```java
public List<Integer> filmsActorPlayedIn(int actorId) {
    String query = "SELECT film_id FROM film_actor WHERE actor_id = :actor_id";
    return namedJdbcTemplate.queryForList(
        query, Collections.singletonMap("actor_id", actorId), Integer.class);
}
```

To refresh your memory on differences between `JdbcTemplate` and this class, take a look at [this](https://stackoverflow.com/a/16362128) answer.