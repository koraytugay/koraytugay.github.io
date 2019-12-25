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
Maven has a global `settings.xml` and a user-specific `settings.xml` that are used for configuration. Installing maven via homebrew, the global `settings.xml` ends up in `/usr/local/Cellar/maven/3.6.1/libexec/conf` and is basically an empty file with documenting the structure of the file. The individiual settings file is found under the `.m2` folder. 

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

we can find out the transitive dependencies via `mvn dependency:tree`:

```bash
[INFO] --- maven-dependency-plugin:2.8:tree (default-cli) @ my-artifact ---
[INFO] biz.tugay:my-artifact:jar:1.0-SNAPSHOT
[INFO] \- junit:junit:jar:4.12:test
[INFO]    \- org.hamcrest:hamcrest-core:jar:1.3:test
```


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

pom.xml
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

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
</project>
```