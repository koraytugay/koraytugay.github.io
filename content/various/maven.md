---
layout: default
title:  "Apache Maven"
---

# Apache Maven
{:.no_toc}

* TOC
{:toc}

## Overview
- Provides a standard [directory layout](https://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html).
- Providers a way to declare dependencies in an external file, the [pom.xml](pom.xml).
- Follows a plug-in based architecture. These plug-ins encapsulate reusable build and task logic.
- Supports archetypes. Maven archetypes are predefined project templates that can be used to generate new projects which contain all of the folders and files needed to get started.

## Settings
Maven has a global `settings.xml` and a user-specific `settings.xml` that are used for configuration. Installing maven via homebrew, the global `settings.xml` ends up in `/usr/local/Cellar/maven/3.6.1/libexec/conf` and is basically an empty file with documenting the structure of the file. The user-specific settings file is found under the `.m2` folder. 

When both files exist, maven merge the contents, user-specific settings taking precedence. 

The structure of the settings.xml is as follows:

```xml
<settings xmlns="http://maven.apache.org/settings/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
          xsi:schemaLocation="http://maven.apache.org/settings/1.0.0  
                              https://maven.apache.org/xsd/settings-1.0.0.xsd">
    <localRepository/>
    <interactiveMode/>
    <offline/>
    <pluginGroups/>
    <servers/>
    <mirrors/>
    <proxies/>
    <profiles/>
    <activeProfiles/>
</settings>
```

A brief explanation of these root elements are as follows:

<dl>
    <dt>
        localRepository
    </dt>
    <dd>
        The default local folder for storing downloaded artifacts is <code class="embedCode">.m2/repository</code>. This folder can be overridden with providing another path here.
    </dd>
    <dt>
        interactiveMode
    </dt>
    <dd>
        Default is <code class="embedCode">true</code>. If switched to <code>false</code> maven does not ask for any input and tries to use sensible defaults.
    </dd>
    <dt>
        offline
    </dt>
    <dd>
        Default is <code class="embedCode">false</code>. Used to force maven to <strong>not</strong> download any dependencies and work in offline mode.
    </dd>
    <dt>
        servers
    </dt>
    <dd>
        Allows the user to specify security credentials for various servers maven connects to.
    </dd>
    <dt>
        mirros
    </dt>
    <dd>
        Forces maven to use mirrored repositories instead of public repositories.
    </dd>
    <dt>
        proxies
    </dt>
    <dd>
        Contains the proxy server settings.
    </dd>
</dl>

## Dependency Management
Maven downloads artifacts and related metadata from remote repositories. The default remote repository is called _Maven Central_ and it is located at [repo.maven.apache.org](repo.maven.apache.org). Maven places a copy of the downloaded artifacts in users local repository, default path being `.m2/repository`.

Dependencies are uniquely identified using __GAV__ coordinates: `groupId`, `artifactId` and `version`.

### Using Different Remote Repositories
Not every artifact is available in the maven central repository and the user may need to define additional repositories. Additional repositories can be defined both in `settings.xml` and in `pom.xml`. In corporate environments, there is almost always a shared repository for artifacts that are not published but required for development. 

### Transitive Dependencies
Transitive dependencies are the dependencies of the dependencies declared in `pom.xml`. For example for a single declared dependency as follows

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
```

we can find out the transitive dependencies as follows:

```bash
mvn dependency:tree

# [INFO] --- maven-dependency-plugin:2.8:tree (default-cli) @ my-artifact ---
# [INFO] biz.tugay:my-artifact:jar:1.0-SNAPSHOT
# [INFO] \- junit:junit:jar:4.12:test
# [INFO]    \- org.hamcrest:hamcrest-core:jar:1.3:test
```

## Project Object Model - pom

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

    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
</project>
```

### Properties
Maven provides placheloders that can be used inside the `pom.xml`. There are two types of properties: implicit properties and user-defined properties.

#### Implicit Properties
Maven exposes properties of `pom.xml` using the `project.` prefix. To access the `artifactId` inside the file, `${project.artifactId}` placeholder can be used. Here is an example:

```xml
<build>
    <finalName>${project.artifactId}</finalName>
</build>
```

To access properties from the `settings.xml` file, the `settings.` prefix is used. For accessing environmental variable values, `env.` prefix can be used. For example, `${env.PATH}` would return the value of the `PATH` environmental variable.

#### User-Defined Properties
Custom properties can be declared within the `<properties />` element. Such properties are usually used for dependency versions.

## Plug-Ins and Goals
Graunlar task are represented by __goal__s in maven. Several goals are packaged into a __plug-in__.

To compile a project, the `compile` goal from the `compiler` plug-in can be used as follows:

```bash
mvn compiler:compile
```

Another example can be using the `clean` goal from the `clean` plug-in, which deletes the `target` folder.

```bash
mvn clean:clean
```

Maven comes with a `help` plug-in that can be used to list available goals in a given plug-in:

```bash
mvn help:describe -Dplugin=surefire

# Name: Maven Surefire Plugin
# Description: Surefire is a test framework project.
# Group Id: org.apache.maven.plugins
# Artifact Id: maven-surefire-plugin
# Version: 2.12.4
# Goal Prefix: surefire
# 
# This plugin has 2 goals:
# 
# surefire:help
#   Description: Display help information on maven-surefire-plugin.
#     Call mvn surefire:help -Ddetail=true -Dgoal=<goal-name> to display
#     parameter details.
# 
# surefire:test
#   Description: Run tests using Surefire.
```

#### Configuring Plug-Ins in pom
Plug-ins and their behavior can be configured using within the pom. Consider the case where you want to enforce that your project must be compiled with Java 8. The following example configures the `compiler` plug-in:

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.8.1</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
    </plugins>
</build>
```

## Build Lifecycle
### Lifecycle and Phases
Every maven project has the following built-in lifecycles: __default__, __clean__ and __site__.

Every lifecycle has several phases. For example, phases of the __default__ lifecycle are as follows:

<dl>
    <dt>
        validate
    </dt>
    <dd>
        Runs checks to ensure that the project is correct and all dependencies are downloaded and available.
    </dd>
    <dt>
        compile
    </dt>
    <dd>
        Compiles the source code.
    </dd>
    <dt>
        test
    </dt>
    <dd>
        Runs tests.
    </dd>
    <dt>
        package
    </dt>
    <dd>
        Assembles compiled code into a distributable format such as JAR or WAR.
    </dd>
    <dt>
        install
    </dt>
    <dd>
        Installs the packaged archive into a local repository. This archive is now available for use by any project sharing the same repository.
    </dd>
    <dt>
        deploy
    </dt>
    <dd>
        Pushes the built archive into a remote repository.
    </dd>
</dl>

__Heads Up!__ A build lifecycle is an abstract concept and __cannot__ be directly executed. What can be executed is either a __lifecycle phase__ or a __goal__.

For all phases of the lifecycles, see the [Lifecycle Reference](http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#Lifecycle_Reference).

```bash
mvn default

# Unknown lifecycle phase "default". 
# You must specify a valid lifecycle phase or a goal in the format 
# <plugin-prefix>:<goal> 
# or 
# <plugin-group-id>:<plugin-artifact-id>[:<plugin-version>]:<goal>.

# Available lifecycle phases are: validate, initialize, generate-sources, 
# process-sources, generate-resources, process-resources, compile, 
# process-classes, generate-test-sources, process-test-sources, 
# generate-test-resources, process-test-resources, test-compile, 
# process-test-classes, test, prepare-package, package, pre-integration-test, 
# integration-test, post-integration-test, verify, install, deploy, pre-clean, 
# clean, post-clean, pre-site, site, post-site, site-deploy.
```

`mvn package` will execute the `package` phase of the `default` lifecycle. In addition to clearly defining the ordering of phases in a lifecycle, maven also automatically executes all the phases prior to a requested phase. When `mvn package` is run all prior phases also run such as `compile` and `test`.

A number of tasks need to be performed in each phase. For that to happen, each `phase` is associated with a zero or more `goal`s.

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
```
