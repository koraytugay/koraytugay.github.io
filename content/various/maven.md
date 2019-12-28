---
layout: default
title:  "Apache Maven"
---

# Apache Maven
{:.no_toc}

* TOC
{:toc}

## POM
In Maven what matters is not just the projects pom file but the effective pom file which is constructed by the project pom file, and the parent pom file hierarchy up to the super pom.

A minimal valid pom file would be as follows:

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

## Plugins and Goals
Maven does not try to do everything itself, but rather delegate the work to to plugins (and goals). __All the useful functionalities in the build process are developed as plugins__. A plugin can contain one to many goals. Goals do specific tasks, be it resolve / download 3<sup>rd</sup> party libraries declared in the pom, compile source code, run tests or package files.

Similar to any other dependency in Maven, a plugin is uniquely identified by three coordinates: `groupId`, `artifactId`, and `version`, a.k.a. __GAV__. However, for plugins, `groupId` is __not__ needed. Maven assumes the following by default: `org.apache.maven.plugins` and `org.codehaus.mojo`.

Maven downloads plugins on demand when it cannot find a plugin in its local repository. By default, Maven looks for any plugin that is not available locally in the plugin repository, defined in the super POM file:

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

### Where are the Plugins Defined?
The super pom already defines several default plugins, such as the `clean` or the `compiler` plugin. Any other plugins are again defined by the user in pom. Check the effective pom to find for example:

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

This is also where plugins are bound to phases.

### Executing a Plugin
A goal from a plugin can be executed on its own or can be executed as a part of a Maven lifecycle. The syntax to execute a goal of Maven plugin is: `mvn plugin-prefix:goal`. The `clean` goal of the `clean` plugin can be executed as follows:

```bash
mvn clean:clean
```

The `clean` goal of the Maven `clean` plugin is associated with the `clean` phase of the `clean` lifecycle, therefore the same `clean` goal can be executed via the `clean` lifecycle as follows, which is somewhat confusing:

```bash
mvn clean
```

One difference here is that when you execute a goal on its own, it only runs the goal specified in the command. When you run it as a part of a lifecycle, all the goals associated with the corresponding lifecycle phases up until the specified phase including that phase gets executed.

### Configuring a Plugin in pom
This is an example on how the `compiler` plugin can be configured:

```xml
<project>
    <build>
        <plugins>
            <plugin>
                <!-- groupId not needed for plugins -->
                <!-- <groupId>org.apache.maven.plugins</groupId> -->
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

A friendly reminider: The `compile` goal is bound to the `compile` phase of the `default` lifecycle.

### Exec Maven Plugin
[Exec Maven Plugin](https://www.mojohaus.org/exec-maven-plugin/index.html) is a very useful for plugin that can be used to quickly run Maven projects in development environment. This section is a showcase on how such an external plugin can be used. Given the following directory layout:

```bash
.
├── pom.xml
└── src
    └── main
        └── java
            └── biz
                └── tugay
                    └── App.java
```

and the following pom:

```xml
<project>

    <modelVersion>4.0.0</modelVersion>
    <groupId>biz.tugay</groupId>
    <artifactId>my-artifact</artifactId>
    <version>1.0-SNAPSHOT</version>

    <build>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>exec-maven-plugin</artifactId>
                <version>1.6.0</version>
                <configuration>
                    <mainClass>biz.tugay.App</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>
</project>
```

`App.java` can be executed by using this plugin via invoking the `java` goal as:

```bash
mvn exec:java
```

This plugin also comes with another goal: `exec`. The official documentation clearly explains the usage, and __this is pretty much what Maven is all about__. Finding plugins, the relevant goals, and configuring / invoking them.

Another super helpful plugin is the [Apache Maven Help Plugin](https://maven.apache.org/plugins/maven-help-plugin/), check it out!

__Heads Up!__ You do not actually need to include `exec-maven-plugin` in the pom. The following works in that case:

```bash
mvn -q exec:java -Dexec.mainClass=biz.tugay.App
```

This, however, does not work with 

```bash
mvn jetty:run
```

Why? I do not know..

### Further Reading
Read the [Maven Plugins](https://maven.apache.org/plugins/) page to get an overall understanding of plugins. Read the [Apache Maven Compiler Plugin](https://maven.apache.org/plugins/maven-compiler-plugin/) documentation to get a better understanding of a specific plugin and the [compile](https://maven.apache.org/plugins/maven-compiler-plugin/compile-mojo.html) goal documentation to get a better understanding of a specific goal.

## Lifecycle
Maven defines a concept called __lifecycle__. A lifecycle would run a single or more __phase__. __Goals from plugins are bound to phases of a lifecycle__. 

## Settings
Start by reading the [Settings Reference](https://maven.apache.org/settings.html).

## Random Notes
- If you are importing any 3<sup>rd</sup> party libraries explicitly via `import` statements in your source code, you need to make sure that the dependency JAR file corresponding to this is being added to the project POM file. You must not rely on transitive dependencies. You can analyze your project using `mvn dependency:analyze`.
- To speed up your build, try `mvn install -T 2C -DskipTests`.
- For future reference, the Maven default lifecycle includes the following phases: 

```
validate -> initialize -> generate-sources -> process-sources 
-> generate-resources -> process-resources -> compile -> process-classes 
-> generate-test-sources -> process-test-sources -> generate-test-resources 
-> process-test- resources -> test-compile -> process-test-classes -> test 
-> prepare- package -> package -> pre-integration-test -> integration-test 
-> post- integration-test -> verify -> install -> deploy
```

- What is the difference between `-DskipTests` and `-Dmaven.test.skip=true`? Visit [this](https://maven.apache.org/surefire/maven-surefire-plugin/examples/skipping-tests.html) page to find out.
- Do you want to create a jar with dependencies. Start [here](https://maven.apache.org/plugins/maven-assembly-plugin/usage.html).


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

# Full information on a plugin
mvn help:describe -Dplugin=exec -Dfull

# Detailed information on a specific goal
mvn help:describe -Dcmd=compiler:compile -Ddetail

# Dependency tree
mvn dependency:tree
```

## References
- [Maven Official Reference](https://maven.apache.org/ref/current/)
- [Default Remote Repository: Maven Central](repo.maven.apache.org)