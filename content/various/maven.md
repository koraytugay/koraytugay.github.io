---
layout: default
title:  "Apache Maven"
---

# Apache Maven
{:.no_toc}

* TOC
{:toc}

## Overview
Maven is a __goal__ execution software. The goals are executed against a `pom.xml` file, which is mandatory for any project to be processed by maven. Every goal is found in a __plugin__. A plugin can contain one to many goals. A goal can do many things, be it resolve / download 3rd party libraries declared in the pom, compile source code, run tests or package files.

A minimal pom file would be as follows:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>biz.tugay</groupId>
    <artifactId>my-artifact</artifactId>
    <version>1.0-SNAPSHOT</version>
</project>
```

Given the following directory layout:

```plaintext
.
├── pom.xml
└── src
    └── main
        └── java
            └── biz
                └── tugay
                    └── App.java
```

We can compile our source code using the `compile` goal from the `compiler` plugin:

```bash
mvn compiler:compile
```

which will produce:

```
.
├── pom.xml
├── src
│   └── main
│       └── java
│           └── biz
│               └── tugay
│                   └── App.java
└── target
    └── classes
        └── App.class
```

Maven also defines a concept called __lifecycle__. A lifecycle would run a single or more __phase__. __Goals from plugins are bound to phases of a lifecycle__. 

Instead of executing a goal, a phase of a lifecycle can also be  executed via `mvn <phase-name>`.

## Plugins
Maven does not try to do everything itself, but rather delegate the work to to plugins. When you download Maven from its website, it's only the core framework. Plugins are downloaded on demand. __All the useful functionalities in the build process are developed as Maven plugins__. Maven plugins can be executed on their own or can be executed as a part of a Maven lifecycle. The syntax to execute a goal of maven plugin is: `mvn plugin-prefix:goal`.

Reading the [Maven Plugins](https://maven.apache.org/plugins/) page to get an overall understanding of plugins. Read the [Apache Maven Compiler Plugin](https://maven.apache.org/plugins/maven-compiler-plugin/) documentation to get a better understanding of a specific plguin and the [compile](https://maven.apache.org/plugins/maven-compiler-plugin/compile-mojo.html) goal documentation to get a better understanding of a specific goal of plugin.

### Configuring a Plugin in pom
This is an example on how the `compiler` plugin can be configured:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
    
    <!-- Elements ommitted for brevity -->

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <verbose>true</verbose>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

## Settings
Start by reading the [Settings Reference](https://maven.apache.org/settings.html).

## Random Notes
- If you have any import statements in a Java class, you need to make sure that the dependency JAR file corresponding to this is being added to the project POM file. You must not rely on transitive dependencies. You can analyze your project using `mvn dependency:analyze`.
- To speed up your build, try `mvn install -T 2C -DskipTests`.

## Cheat Sheet

```bash
# Creating an Empty Maven Project Using the Quickstart Archetype
# Reference: https://maven.apache.org/archetypes/maven-archetype-simple
mvn archetype:generate                               \
    -DarchetypeGroupId=org.apache.maven.archetypes   \
    -DarchetypeArtifactId=maven-archetype-quickstart \
    -DarchetypeVersion=1.4

# Creating the standard directory layout
mkdir -p src/main/java/biz/tugay
mkdir -p src/test/java/biz/tugay

# Effective pom
mvn help:effective-pom

# Seeing goals of a specific plugin
mvn help:describe -Dplugin=surefire

# Dependency tree
mvn dependency:tree
```

## References
- [Maven Official Reference](https://maven.apache.org/ref/current/)
- [Default Remote Repository: Maven Central](repo.maven.apache.org)