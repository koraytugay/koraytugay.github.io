---
layout: default
title:  "Dockerfile"
---

# Dockerfile
{:.no_toc}

* TOC
{:toc}

## Overview
A [Dockerfile](https://docs.docker.com/v17.12/engine/reference/builder/) is a text-based build script from which images can be built. The instructionso of Dockerfile can contain:

- Selecting the base image
- Installing the required application
- Adding the configuration and data files
- Commands to running services
- Commands to expose the services to outside

A Dockerfile is in the form of

```dockerfile
# This is a comment.
INSTRUCTION argument(s)
```

where instructions are case-insensitive but best practice is to have them uppercase.

## Dockerfile Example
Given a Dockerfile with following contents:

```dockerfile
FROM busybox
CMD echo Hello world!
```

an image can be built by 

```bash
docker build -t koraytugay/busyhello .
````

## Dockerfile Instructions
### FROM
`FROM` is the first valid instruction of a Dockerfile and sets the base image for the build process. The syntax is: `FROM <image>[:<tag>|@digest]`. Value `latest` is the default if no `tag` or `digest` is supplied. `FROM centos` and `FROM ubuntu:16.04` are examples for the `FROM` instruction.

Using multiple `FROM` instructions is possible but discouraged.

### COPY
`COPY <src> ... <dst>` can be used to copy multiple files (or directories) from the Docker host environment to the image, as in `COPY html /var/www/html` or `COPY file1.txt file2.txt /`.

__Example__
The following Dockerfile will copy itself into the built image, and will print the contents when the image is run.

```dockerfile
FROM busybox
COPY Dockerfile /
CMD cat Dockerfile
```

### ADD
Please see [this](https://stackoverflow.com/questions/24958140) question on Stackoverflow for the differences between `COPY` and `ADD`.

### ENV
`ENV <key> <value>` sets environmental variables in the image. 

```dockerfile
ENV APACHE_LOG_DIR /var/log/apache
```

Values declared by `ENV` can also be used together with other instructions.

```dockerfile
ENV BACKUP_DIR backupdir
WORKDIR /usr/${BACKUP_DIR}
```

### ARG
`ARG <variable> [=<default value>]` lets defining variables that can be passed during Docker image build time. 

Given the following Dockerfile:

```dockerfile
ARG user=john_doe
```

an image can be built overriding the value `johndoe` by:

```bash
docker build --build-arg user=koraytugay .
```

Just like `ENV` , declarations can be used together with other instructions.

### WORKDIR
`WORKDIR` changes the current working directory from `/` to path specified. This will affect `RUN` and `CMD` instructions. The path can be relative or absolute. In case it is relative, it is relative to the last `WORKDIR` set. If the directory does not exist, it will be created.

```dockerfile
WORKDIR /mycustom/workdir
```

### VOLUME 
todo

### EXPOSE
`EXPOSE <port> [<port>...]` lets you expose multiple ports from the container.

### LABEL
Metadata can be added to images using `LABEL`.

```dockerfile
LABEL version="2.0"
      release-date="2016-08-05"
```

### RUN
`RUN` is executed when an image is being built and can be used to run any command. Best practice is to execute multiple commands using one `RUN` instruction. `RUN` command has two types of syntax:

#### Shell Type
`RUN <command>` is the shell command to be executed. This type is executed using `/bin/sh -c`. `-c` is used for reading commands from string:

```dockerfile
FROM alpine
RUN touch foo.txt
```

#### JSON array
`RUN ["<exec", "<arg-1>", ... "<arg-n>"]` where `exec` is the executable to run followed by any number of arguments.

```dockerfile
FROM alpine
RUN ["sh", "-c", "touch foo.txt"]
```

### CMD
`CMD` is somewhat similar to `RUN` with a one big very difference: execution time. `CMD` is executed when the container is launched. `CMD` instruction provides the default execution for the container. When the supplied application terminates, the container also terminates. 