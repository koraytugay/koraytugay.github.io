---
layout: default
title:  "Dockerfile"
---

# Dockerfile
{:.no_toc}

* TOC
{:toc}

## Overview
A [Dockerfile](https://docs.docker.com/v17.12/engine/reference/builder/) is a text-based build script from which images can be built. The instructions of Dockerfile can contain:

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

where instructions are case-insensitive but best practice is to have them uppercase. Given a Dockerfile with following contents:

```dockerfile
FROM busybox
CMD echo Hello world!
```

an image can be built by:

```bash
docker build -t koraytugay/busyhello .
````

Another advantage of using Dockerfiles is, they integrate easily with existing build systems and continuous integration tools.

## Dockerfile Instructions
### FROM
`FROM` is the first valid instruction of a Dockerfile and sets the base image for the build process. Value `latest` is the default if no `tag` or `digest` is supplied. `FROM centos` and `FROM ubuntu:16.04` are examples for the `FROM` instruction. 

### ENV
To refresh your memory on environmental variables, see [this](https://askubuntu.com/a/58828) answer.

`ENV` is used to set environment variables, either to be used in the Dockerfile itself or in the image being created. They can also be overridden when running a container.

Starting with the following Dockerfile:

```dockerfile
FROM alpine
ENV msg="Hello World"
CMD echo $msg
```

Build and run to see the `msg`:

```bash
docker build -t alpine-hello .
docker run --rm alpine-hello
# Hello World
```

You can also overridde the environmental variable `msg` as follows:

```bash
docker run --rm --env msg="Bye Bye" alpine-hello
# Bye Bye
```

### COPY
`COPY <src> ... <dst>` can be used to copy files from the host environment to the image, as in `COPY html /var/www/html` or `COPY file1.txt file2.txt /`. The following Dockerfile will copy itself into the built image, and will print the contents when the image is run.

```dockerfile
FROM busybox
COPY Dockerfile /
CMD cat Dockerfile
```

### ADD
Please see [this](https://stackoverflow.com/questions/24958140) question on Stackoverflow for the differences between `COPY` and `ADD`.

### WORKDIR
`WORKDIR` changes the current working directory from `/` to path specified. This will affect `RUN`, `CMD` and `ENTRYPOINT` instructions. The path can be relative or absolute. In case it is relative, it is relative to the last `WORKDIR` set. If the directory does not exist, it will be created.

```dockerfile
WORKDIR /mycustom/workdir
```

### EXPOSE
> The `EXPOSE` instruction does not actually publish the port. 
>
> It functions as a type of documentation between the person who builds the image and the person who runs the container, about which ports are intended to be published. 
>
> To actually publish the port when running the container, use the `-p` flag on docker run to publish and map one or more ports, or the `-P` flag to publish all exposed ports and map them to high-order ports.

### RUN
`RUN` is executed when an image is being built and can be used to run any command. Best practice is to execute multiple commands using one `RUN` instruction since every execution adds a new layer to the image. 

Installing software packages is one of the most common use cases for the `RUN` instruction. 

`RUN` has two forms: Shell Form and Exec Form.

#### Shell Form
Shell Form has the syntax: `RUN <command>` and is defaults to `/bin/sh -c` where `-c` is used for reading commands from a string:

```dockerfile
FROM alpine
RUN touch foo.txt
```

#### Exec Form
Exec form has the syntax: `RUN ["<exec>", "<arg-1>", ..., "<arg-n>"]` where `exec` is the executable to run followed by any number of arguments. This form also lets the user use a different shell:

```dockerfile
FROM alpine
RUN ["/bin/bash", "-c", "touch foo.txt"]
```

### CMD
`CMD` is similar to `RUN` with one major difference: execution time: `CMD` is executed when the container is launched. `CMD` can be overridden by `docker run <sub-command>`.

> __The main purpose of a `CMD` is to provide defaults for an executing container.__ These defaults can include an executable, or they can omit the executable, in which case you must specify an `ENTRYPOINT` instruction as well.

> If `CMD` is used to provide default arguments for the `ENTRYPOINT` instruction, both the `CMD` and `ENTRYPOINT` instructions should be specified with the JSON array format.

> When used in the shell or exec formats, the `CMD` instruction sets the command to be executed when running the image.

> If you use the shell form of the `CMD`, then the `<command>` will execute in `/bin/sh -c`.

> If the user specifies arguments to `docker run` then they will override the default specified in `CMD`.

#### Shell Form
`CMD <command>` is the syntax for the Shell form. This type is executed using `/bin/sh -c`. 

#### Exec Form
Syntax for this form is `CMD ["<executable>", "<arg-1>", ..., "<arg-n>"]` where `exec` is the executable to run followed by any number of arguments. 

> If you want to run your `<command>` without a shell then you must express the command as a JSON array and give the full path to the executable. __This is the preferred form__.

```bash
FROM ubuntu
CMD ["/usr/bin/wc","--help"]
```

#### Defaults to ENTRYPOINT
Syntax for this form is `CMD ["<arg-1>", ..., "<arg-n>"]` and this form is used for setting the default parameters to the `ENTRYPOINT` instructions instead of providing an executable. If this form is used, both the `CMD` and `ENTRYPOINT` instructions should be specified with the exec form.

### ENTRYPOINT
`ENTRYPOINT` is similar to `CMD`, however the sub-commands following `docker run` are treated as arguments to `ENTRYPOINT` instead of overriding as it was the case for `CMD`. `ENTRYPOINT` can also be overridden by `docker run --entrypoint <sub-command>`.

`ENTRYPOINT` command has two forms:

#### Shell Form
`ENTRYPOINT <command>` is the shell command to be executed. This type is executed using `/bin/sh -c`.

#### Exec Form
`ENTRYPOINT ["<exec", "<arg-1>", ..., "<arg-n>"]` where `exec` is the executable to run followed by any number of arguments. __This is the prefered form__.

### Understanding ENTRYPOINT and CMD
Refresh your memory by looking at [this](https://docs.docker.com/v17.12/engine/reference/builder/#understand-how-cmd-and-entrypoint-interact) page over and over.