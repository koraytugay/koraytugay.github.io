---
layout: default
title:  "Apache Maven"
---

# Apache Maven
{:.no_toc}

* TOC
{:toc}

## POM
The POM is where a projects identity is declared, builds are configured, and dependencies are defined. __The presence of a `pom.xml` defines a Maven project__. POM tells Maven how to modify default behavior to generate output from source.

A minimal valid POM would be as follows:

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

### Super POM
All Maven POMs inherit values from a parent POM. If a POM does not specify a direct parent using the `parent` element, that POM will inherit values from the __super POM__ (which comes by default with Maven). Whatever configuration you need to override, you can do it by redefining the same section in your application POM file.

### Effective POM
In Maven what matters is not just the projects POM but the effective POM which is constructed by the project POM, and the parent POM file hierarchy up to the super POM. This is called the effective POM. 

## Dependencies
When a project depends on an artifact produced by another project, this artifact is said to be a __dependency__.

Maven downloads artifacts and related metadata from remote repositories and places a copy of the downloaded artifacts in users local repository, default path being __~/.m2/repository__.

Super POM defines a default remote repository: the [Maven Central Repository](https://repo.maven.apache.org/maven2/), that all clients are configured to read from by default. The central repository also contains Maven plugins, in other words: the default repository for plugins is also the Maven Central Repository.

### Maven Coordinates
Dependencies are uniquely identified using coordinates named __GAV__: __Group ID__, __Artifact ID__ and __Version__. Adding a dependency to a Maven project is as simple as follows:

```xml
<dependencies>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-lang3</artifactId>
        <version>3.9</version>
    </dependency>
</dependencies>
```

### Transitive Dependencies
__A transitive dependency is a dependency of a dependency.__ If project-a depends on project-b, which in turn depends on project-c, _project-c is considered to be a transitive dependency of project-a_. Maven manages transitive dependencies and keeps track of all of the dependencies required to compile and run an application by building a graph of dependencies and dealing with any conflicts and overlaps. If two projects depend on the same `groupId` and `groupId`, Maven will sort out which dependency to use always favoring the more recent version.

Transitive dependencies can be excluded and/or replaced as follows:

```xml
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate</artifactId>
    <version>3.2.5</version>
    <exclusions>
        <exclusion>
            <groupId>javax.transaction</groupId>
            <artifactId>jta</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.apache.geronimo.specs</groupId>
    <artifactId>geronimo-jta_1.1</artifactId>
    <version>1.1</version>
</dependency>
```

## Plugins
Maven does not do much itself, but rather delegate the work to plugins (or goals in plugins to be more precise). __All the useful functionalities in the build process are developed as plugins__. A plugin can contain one to many goals. Goals do specific tasks such as:

- Resolve / download dependencies declared in the POM
- Compile source code
- Run tests 
- Package files

Similar to any other dependency in Maven, plugins are also identified via GAV. However, for plugins, `groupId` is __not__ needed. Maven assumes the following for plugins by default: `org.apache.maven.plugins` and `org.codehaus.mojo`.

The super POM already defines several default plugins, such as the __clean__ or the __compiler__ plugins. Any further plugins can be defined by user in POM. Check the effective POM to find for example:

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

_The `plugin` element is also where a plugin is bound to a phase of lifecycle. More on this later._

### Executing a Plugin Goal
A goal from a plugin can be executed on using the following syntax:

```bash
mvn plugin-prefix:goal
```

For example, the __clean__ goal of the __clean__ plugin can be executed as follows:

```bash
mvn clean:clean
```

__Getting a little ahead of ourselves here:__ The __clean__ goal of the Maven __clean__ plugin is bound with the __clean__ phase of the __clean__ lifecycle by default, therefore the same __clean__ goal can be executed as follows:

```bash
mvn clean
```

We are invoking the __clean__ phase here (which is a part of the __clean__ lifecycle).One difference here is that when you execute a goal on its own, it only runs the goal specified in the command. When you run a phase, all the goals associated with the corresponding lifecycle phases up until the specified phase including that phase gets executed, which means in our case all plugins bound to __pre-clean__ would be executed.

### Configuring a Plugin
Very often plugins will have options to configure them on have they behave. This is an example on how the __compiler__ plugin can be configured:

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

#### Configuring a Plugin using `pluginManagement`
Another possibility to configure / specify versions for plugins in POM is to use the `pluginManagement` section:

```xml
<project>

    <modelVersion>4.0.0</modelVersion>
    <groupId>biz.tugay</groupId>
    <artifactId>delete-me</artifactId>
    <version>1.0-SNAPSHOT</version>

    <build>
        <pluginManagement>
            <plugins>
                <plugin>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.8.1</version>
                    <configuration>
                        <source>1.8</source>
                        <target>1.8</target>
                        <verbose>true</verbose>
                    </configuration>
                </plugin>
                <plugin>
                    <groupId>org.codehaus.mojo</groupId>
                    <artifactId>exec-maven-plugin</artifactId>
                    <version>1.6.0</version>
                    <configuration>
                        <mainClass>biz.tugay.App</mainClass>
                    </configuration>
                    <executions>
                        <execution>
                            <phase>verify</phase>
                            <goals>
                                <goal>java</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
            </plugins>
        </pluginManagement>

        <plugins>
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>exec-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
</project>
```

In the example above, the configuration for __compiler__ plugin is immediatly effective by only configuring in `pluginManagement` section. It is, however, not the case for the __exec__ plugin, it must be declared under `plugins`.

The `pluginManagement` is a bit messy and the [official documentation](https://maven.apache.org/pom.html#Plugin_Management) does not help much and unfortunately is quite poor. Here is what I think is happening:

The __compiler plugin__ above does not need to be declared under `build/plugins` because it is already defined in the super POM. However, the __exec plugin__ is not found by default in the super POM, hence it must be re-declared in the very same POM.

#### Plugin Configuration Example with Exec Plugin
[Exec Maven Plugin](https://www.mojohaus.org/exec-maven-plugin/index.html) is a very useful for plugin that can be used to quickly run Maven projects in development environment. Given the following directory layout:

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

and the following POM:

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

This plugin also comes with another goal: `exec`. The official documentation clearly explains the usage, and __this is pretty much what Maven is all about__. Finding plugins, the relevant goals, configuring them and then either invoking them or attaching them to lifecycle phases.

__Heads Up!__ You do not actually need to include `exec-maven-plugin` in the POM. The following works in that case:

```bash
mvn -q exec:java -Dexec.mainClass=biz.tugay.App
```

This, however, does not work with 

```bash
mvn jetty:run
```

Why? I think because jetty-plugin has a different `groupId`.

### Investigating Plugins using the Help Plugin
[Apache Maven Help Plugin](https://maven.apache.org/plugins/maven-help-plugin/) can be used to learn more about goals and configuration options of a selected plugin. Here is the cheat sheet you need:

```bash
# Describe a plugin
mvn help:describe -Dplugin=jetty

# Describe a plugin detailed
mvn help:describe -Dplugin=jetty -Ddetail

# Describe a specific goal
mvn help:describe -Dcmd=jetty:run

# Describe a specific goal in detail
mvn help:describe -Dcmd=jetty:run -Ddetail
```

### Further Reading
Read the [Maven Plugins](https://maven.apache.org/plugins/) page to get an overall understanding of plugins. Read the [Apache Maven Compiler Plugin](https://maven.apache.org/plugins/maven-compiler-plugin/) documentation to get a better understanding of a specific plugin and the [compile](https://maven.apache.org/plugins/maven-compiler-plugin/compile-mojo.html) goal documentation to get a better understanding of a specific goal.

## Lifecycle
Maven defines a concept called __lifecycle__. A lifecycle has many <strong>phase</strong>s. __Goals from plugins are bound to phases of a lifecycle__. There are three standard lifecycles in Maven: __clean__, __default__ (a.k.a. __build__) and __site__.

__A lifecycle on its own cannot be invoked but a phase of a lifecycle can be.__ For example: invoking `mvn install` will invoke all goals bound to the phases until (and including) the __install__ phase of the __default lifecycle__.

__Friendly Reminder!__ You can either invoke a specific goal of a plugin, or a phase of a lifecycle. Here are two examples:

```bash
# Invokes clean goal from clean plugin
mvn clean:clean

# Invokes goals bound to pre-clean and clean phases of the clean lifecycle
mvn clean
```

### Clean Lifecycle as an Example
This lifecycle consists of three phases: __pre-clean__, __clean__ and __post-clean__. The interesting phase in the clean lifecycle is the clean phase. [Apache Maven Clean Plugin](https://maven.apache.org/plugins/maven-clean-plugin/)s clean goal is bound to the clean phase in the clean lifecycle. This goal deletes the output of a build by deleting the build directory.

__Heads Up!__ If you bind a goal to __post-clean__ phase of the clean build, and simply call `mvn clean`, your goal will not get executed, which is super weird. I think instead of being able to invoke a phase, a lifecycle in complete should have been possible to invoke. In other words, I think it would have made much more sense had `mvn clean` invoked the whole __clean lifecycle__, instead of invoking __pre-clean__ and __clean__ phases only, (skipping __post-clean__). This way it would have also been possible to for example invoke `mvn build` and invoke the whole __build lifecycle__.

### Binding a Plugin to a Phase
Binding a goal of a plugin to a lifecycle phase is as easy as adding the `execution` element in the `plugin`. The following binds the __exec plugin__ to __verify__ phase of the default lifecycle:

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>1.6.0</version>
    <configuration>
        <mainClass>biz.tugay.App</mainClass>
    </configuration>
    <executions>
        <execution>
            <phase>verify</phase>
            <goals>
                <goal>java</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

### Package-Specific Lifecycles
The specific goals bound to each phase default to a set of goals specific to a project's packaging. A project with packaging jar for example has the `jar:jar` bound to the package phase where as a project with packaging war has `war:war` bound. In other words: `packaging` element has an impact on the default plugins that are bound to the lifecycle. 

## Build Profiles
Profiles allow for the ability to customize a particular build for a particular environment profiles.

A common practice is to have __development__ and __production__ profiles. For example, the production build may have a specific build name pattern, may skip tests and so on and so forth. The following is an example where profile __prod__ configures the tests to be skipped:


```xml
<project>
    <profiles>
        <profile>
            <id>prod</id>
            <build>
                <plugins>
                    <plugin>
                        <artifactId>maven-surefire-plugin</artifactId>
                        <version>2.12.4</version>
                        <configuration>
                            <skipTests>true</skipTests>
                        </configuration>
                    </plugin>
                </plugins>
            </build>
        </profile>
    </profiles>
</project>
```

### Activating Profiles
Profiles can be activated via command line:

```bash
mvn clean install -P prod
```

Profiles can also be activated in settings.xml file:

```xml
<settings>
    <activeProfiles>
        <activeProfile>prod</activeProfile>
    </activeProfiles>
</settings>
```

A profile can also be active by default by having:

```xml
<activation>
    <activeByDefault>true</activeByDefault>
</activation>
```

Another common approach for activating a profile is depending on existence or absence of a system property. Given the following profile declaration:

```xml
<profile>
  <id>docker-functional-tests</id>
  <activation>
    <property>
      <name>run-functional-tests</name>
      <value>docker</value>
    </property>
  </activation>
</profile>
```

docker-functional-tests profile declared above can be activated via

```bash
mvn -Drun-functional-tests=docker
```

Further reading is here: [Introduction to Build Profiles](https://maven.apache.org/guides/introduction/introduction-to-profiles.html).

## Properties
Maven properties are variables enclosed in curly braces, prefixed with a dollar sign: `${property}`. 

### Implicit Properties
Maven provides three implicit variables which can be used to access environment variables, POM information, and Maven Settings.

<dl>
    <dt><span class="embedCode">env</span></dt>
    <dd>The <span class="embedCode">env</span> variable exposes environment variables exposed by your operating system or shell. For example, a reference to <span class="embedCode">${env.PATH}</span> in POM would be replaced by the <span class="embedCode">${PATH}</span> environment variable.</dd>
    <dt><span class="embedCode">project</span></dt>
    <dd>The <span class="embedCode">project</span> variable exposes the POM. You can use a dot-notated (.) path to reference the value of a POM element. For example <span class="embedCode">${project.groupId}</span> can be used to reference the <span class="embedCode">groupId</span> element.</dd>
    <dt><span class="embedCode">settings</span></dt>
    <dd>The <span class="embedCode">settings</span> variable exposes Maven settings information. You can use a dot-notated (.) path to reference the value of an element in a settings.xml file. <span class="embedCode">${settings.offline}</span> would reference the value of the <span class="embedCode">offline</span> element in the settings file.</dd>
</dl>

### User Defined Properties
Arbitrary properties can also be set using the `properties` element in the POM and can be simply referenced via `${property}`.

### Properties Example
The following POM demonstarates: 

- Declaring a custom property
- Using the implicit property: `env`
- Binding a custom plugin to `pre-clean` phase

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

    <build>
        <plugins>
            <plugin>
                <groupId>com.github.ekryd.echo-maven-plugin</groupId>
                <artifactId>echo-maven-plugin</artifactId>
                <version>1.2.0</version>
                <executions>
                    <execution>
                        <phase>pre-clean</phase>
                        <goals>
                            <goal>echo</goal>
                        </goals>
                        <configuration>
                            <message>${message} ${env.JAVA_HOME}</message>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <message>Java HOME:</message>
    </properties>
</project>
```

When invoked via `mvn pre-clean` in my environment, I see:

```plaintext
Scanning for projects...

-----------------------< biz.tugay:my-artifact >------------------------
Building my-artifact 1.0-SNAPSHOT
--------------------------------[ jar ]---------------------------------

--- echo-maven-plugin:1.2.0:echo (default) @ my-artifact ---
Java HOME: /Library/Java/JavaVirtualMachines/adoptopenjdk-8.jdk/Contents/Home
------------------------------------------------------------------------
BUILD SUCCESS
------------------------------------------------------------------------
Total time:  0.245 s
Finished at: 2020-01-04T11:02:00-05:00
------------------------------------------------------------------------
```

## Resource Filtering
Maven can be configured to replace variables on arbitrary resource files during a build. See [Filtering](https://maven.apache.org/plugins/maven-resources-plugin/examples/filter.html) to get started. Here is an example, given the following layout:

```bash
.
├── pom.xml
└── src
    └── main
        └── resources
            └── application.properties
```

and a single entry in `application.properties`

```properties
foo=${foo}
```

Resource filtering in POM can be configured as follows:

```xml
<project>
    <build>
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <filtering>true</filtering>
            </resource>
        </resources>
    </build>    
    <properties>
        <foo>This is foo!</foo>
    </properties>
</project>
```

When the goal itself is explicitly invoked via `mvn resources:resources` or the default lifecycle phase is invoked via `mvn process-resources`, `application.properties` will be in `target/classes` with _This is foo!_ being replaced for `${foo}`.

### Resource Filtering From External Files
The example above replaces values hard-coded into POM in the resources being filtered. Values can be fetched from external files using the `filter` element. Here is an example. Given the following directory layout:

```plaintext
.
├── pom.xml
├── profiles
│   ├── dev
│   │   └── config.properties
│   └── prod
│       └── config.properties
└── src
    └── main
        └── resources
            └── application.properties
```

Contents of the `profiles/dev/config.properties`

```properties
config.val=dev-value
```

And his sibling in `prod` folder is as follows:

```properties
config.val=prod-value
```

And `application-properties` as follows:

```properties
app.prop=${config.val}
```

Here is how POM can be configured to whether replace the `app.prop` from the configuration file found in `dev` or `prod` folder depending on the profile being used:

```xml
<project>
    <profiles>
        <profile>
            <id>dev</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <properties>
                <config.dir>dev</config.dir>
            </properties>
        </profile>
        <profile>
            <id>prod</id>
            <properties>
                <config.dir>prod</config.dir>
            </properties>
        </profile>
    </profiles>

    <build>
        <filters>
            <filter>profiles/${config.dir}/config.properties</filter>
        </filters>
        <resources>
            <resource>
                <filtering>true</filtering>
                <directory>src/main/resources</directory>
            </resource>
        </resources>
    </build>
</project>
```

Try building the project with `-P dev` and `-P prod` to see the difference for yourself.

## Settings
Start by reading the [Settings Reference](https://maven.apache.org/settings.html).

## Multi-Module Projects
Multi-module projects are common in enterprise environments. Here is a sample multi-module project layout, and some related notes.

Directory layout:

```plaintext
.
├── multi-module-cli
│   └── pom.xml
├── multi-module-service
│   └── pom.xml
└── pom.xml
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
    <artifactId>multi-module</artifactId>
    <version>1.0-SNAPSHOT</version>

    <packaging>pom</packaging>

    <modules>
        <module>multi-module-service</module>
        <module>multi-module-cli</module>
    </modules>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

</project>
```

multi-module-service/pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>biz.tugay</groupId>
        <artifactId>multi-module</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    
    <!-- groupId from parent -->
    <artifactId>multi-module-service</artifactId>
    <version>1.0-SNAPSHOT</version>

    <packaging>jar</packaging>

</project>
```

multi-module-cli/pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>
   
    <parent>
        <groupId>biz.tugay</groupId>
        <artifactId>multi-module</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>

    <!-- groupId from parent -->
    <artifactId>multi-module-cli</artifactId>
    <version>1.0-SNAPSHOT</version>
    
    <packaging>jar</packaging>
    
    <dependencies>
        <dependency>
            <groupId>biz.tugay</groupId>
            <artifactId>multi-module-service-override</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>                    
    </dependencies>

</project> 
```

### Notes on Multi-Module Projects
- A multi-module project is a defined by a parent POM referencing one or more submodules.
- The packaging of the parent pom is of type `pom`.
- Each submodule lives in the directory found in `module` element.
- Parent POM is the perfect place for declaring dependency management, shared dependencies or plug-in versions.
- When Maven is executed against a project with submodules, Maven first loads the parent POM and locates all of the submodules. All of the modules are put in something called the __Reactor__. Reactor takes care of ordering components to ensure that interdependent modules are compiled and installed in the proper order.
  - Once the Reactor figures out the order in which projects must be built, Maven then executes the specified goals for every module in a multi-module build. 

## Random Notes
- If you are importing any 3<sup>rd</sup> party libraries explicitly via `import` statements in your source code, you need to make sure that the dependency JAR file corresponding to this is being added to the project POM file. You must not rely on transitive dependencies. You can analyze your project using `mvn dependency:analyze`.
- To speed up your build, try using multiple cores: `mvn install -T 2C`.
- For future reference, the Maven default lifecycle includes the following phases: 

```
validate -> initialize -> generate-sources -> process-sources 
-> generate-resources -> process-resources -> compile -> process-classes 
-> generate-test-sources -> process-test-sources -> generate-test-resources 
-> process-test-resources -> test-compile -> process-test-classes -> test 
-> prepare-package -> package -> pre-integration-test -> integration-test 
-> post-integration-test -> verify -> install -> deploy
```

- What is the difference between `-DskipTests` and `-Dmaven.test.skip=true`? Visit [this](https://maven.apache.org/surefire/maven-surefire-plugin/examples/skipping-tests.html) page to find out.
- Do you want to create a jar with dependencies? Start [here](https://maven.apache.org/plugins/maven-assembly-plugin/usage.html).
- The Surefire plugin is Maven's unit testing plugin, the default behavior of Surefire is to look for all classes ending in `Test` in the test source directory and to run them as JUnit tests. The Surefire plugin can also be configured to run TestNG unit tests.

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

# Effective POM
mvn help:effective-pom

# Printing active profiles
mvn help:active-profiles

# Dependency tree
mvn dependency:tree
```

## References
- [Maven Official Reference](https://maven.apache.org/ref/current/)
- [POM Reference](https://maven.apache.org/pom.html)
- [Settings Reference](https://maven.apache.org/settings.html)