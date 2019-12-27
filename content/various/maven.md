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

## POM
In maven what matters is not just the projects pom file but the effective pom file which is constructed by the project pom file, and the parent pom file hierarchy up to the super pom.

## Plugins
Maven does not try to do everything itself, but rather delegate the work to to plugins. __All the useful functionalities in the build process are developed as Maven plugins__. 

Similar to any other dependency in Maven, a plugin is uniquely identified by three coordinates: `groupId`, `artifactId`, and `version`. However, for plugins, groupId is __not__ needed. Maven assumes the following by default: `org.apache.maven.plugins` and `org.codehaus.mojo`.

Maven downloads plugins on demand when it cannot find a plugin in its local repository. By default, maven looks for any plugin that is not available locally in the Maven plugin repository defined by the super POM file.

```xml
<pluginRepositories>
    <pluginRepository>
        <releases>
            <updatePolicy>never</updatePolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
        </snapshots>
        <id>central</id>
        <name>Central Repository</name>
        <url>https://repo.maven.apache.org/maven2</url>
    </pluginRepository>
</pluginRepositories>
```

### Executing a Plugin
Plugins can be executed on their own or can be executed as a part of a Maven lifecycle. The syntax to execute a goal of maven plugin is: `mvn plugin-prefix:goal`. The `clean` goal of the `clean` plugin can be executed as follows:

```bash
mvn clean:clean
```

The `clean` goal of the maven `clean` plugin is associated with the `clean` phase of the `clean` lifecycle, therefore the same `clean` goal can be executed via the `clean` lifecycle as follows:

```bash
mvn clean
```

One difference here is that when you execute a goal on its own, it only runs the goal specified in the command.When you run it as a part of a lifecycle, all the plugins associated with the corresponding lifecycle up until the specified phase including that phase gets executed.

### Where are the Plugins Defined?
Most likely in the parent (or the super) POM. Check the effective pom to find for example:

```xml
<build>
    <plugins>
        <plugin>
            <artifactId>maven-clean-plugin</artifactId>
            <version>2.5</version>
            <executions>
                <execution>
                    <id>default-clean</id>
                    <phase>clean</phase>
                    <goals>
                        <goal>clean</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>    
```

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
                <version>3.8.1</version> <!-- version can be ommitted -->
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

A friendly reminider: The `compile` goal is bound to the `compile` phase of the `default` lifecycle.

Read the [Maven Plugins](https://maven.apache.org/plugins/) page to get an overall understanding of plugins. Read the [Apache Maven Compiler Plugin](https://maven.apache.org/plugins/maven-compiler-plugin/) documentation to get a better understanding of a specific plugin and the [compile](https://maven.apache.org/plugins/maven-compiler-plugin/compile-mojo.html) goal documentation to get a better understanding of a specific goal of plugin.

## Settings
Start by reading the [Settings Reference](https://maven.apache.org/settings.html).

## Random Notes
- If you have any import statements in a Java class, you need to make sure that the dependency JAR file corresponding to this is being added to the project POM file. You must not rely on transitive dependencies. You can analyze your project using `mvn dependency:analyze`.
- To speed up your build, try `mvn install -T 2C -DskipTests`.
- The Maven default lifecycle includes the phases: validate -> initialize -> generate-sources -> process-sources -> generate-resources -> process-resources -> compile -> process-classes -> generate-test-sources -> process-test-sources -> generate-test-resources -> process-test- resources -> test-compile -> process-test-classes -> test -> prepare- package -> package -> pre-integration-test -> integration-test -> post- integration-test -> verify -> install -> deploy.

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