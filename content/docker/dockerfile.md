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

where instructions are case-insensitive but best practice is to have them uppercase. Given a Dockerfile with following contents:

```dockerfile
FROM busybox
CMD echo Hello world!
```

an image can be built by:

```bash
docker build -t koraytugay/busyhello .
````

## Dockerfile Instructions
### FROM
`FROM` is the first valid instruction of a Dockerfile and sets the base image for the build process. Value `latest` is the default if no `tag` or `digest` is supplied. `FROM centos` and `FROM ubuntu:16.04` are examples for the `FROM` instruction. 

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
`WORKDIR` changes the current working directory from `/` to path specified. This will affect `RUN` and `CMD` instructions. The path can be relative or absolute. In case it is relative, it is relative to the last `WORKDIR` set. If the directory does not exist, it will be created.

```dockerfile
WORKDIR /mycustom/workdir
```

### VOLUME 
todo

### EXPOSE
Please see [this](https://stackoverflow.com/a/47594352/1173112) answer for `EXPOSE`.

### RUN
`RUN` is executed when an image is being built and can be used to run any command. Best practice is to execute multiple commands using one `RUN` instruction since every execution adds a new layer to the image. 

`RUN` has two forms: Shell Form and Exec Form.

#### Shell Form
Shell Form has the syntax: `RUN <command>` and is defaults to `/bin/sh -c` where `-c` is used for reading commands from string:

```dockerfile
FROM alpine
RUN touch foo.txt
```

#### Exec Form
Exec form has the syntax: `RUN ["<exec", "<arg-1>", ..., "<arg-n>"]` where `exec` is the executable to run followed by any number of arguments. This form also lets using a different shell:

```dockerfile
FROM alpine
RUN ["/bin/bash", "-c", "touch foo.txt"]
```

### CMD
`CMD` is similar to `RUN` with one major difference: execution time: `CMD` is executed when the container is launched. `CMD` can be overridden by `docker run <sub-command>`.

> __The main purpose of a `CMD` is to provide defaults for an executing container.__ These defaults can include an executable, or they can omit the executable, in which case you must specify an `ENTRYPOINT` instruction as well.

> Unlike the shell form, the exec form does not invoke a command shell. This means that normal shell processing does not happen. For example, `CMD [ "echo", "$HOME" ]` will not do variable substitution on `$HOME`. If you want shell processing then either use the shell form or execute a shell directly, for example: `CMD [ "sh", "-c", "echo $HOME" ]`. When using the exec form and executing a shell directly, as in the case for the shell form, it is the shell that is doing the environment variable expansion, not docker.

#### Shell Form
`CMD <command>` is the shell command to be executed. This type is executed using `/bin/sh -c`. 

#### Exec Form
`CMD ["<executable>", "<arg-1>", ..., "<arg-n>"]` where `exec` is the executable to run followed by any number of arguments. __This is the preferred form__.

#### Defaults to ENTRYPOINT
`CMD ["<arg-1>", ..., "<arg-n>"]` is used for setting the default parameters to the `ENTRYPOINT` instructions instead of providing an executable. 

If this form is used, both the `CMD` and `ENTRYPOINT` instructions should be specified with the exec form.

#### Examples
The following are valid options:

```dockerfile
ENTRYPOINT ["java"]
CMD ["Hello"]
```

```dockerfile
CMD java Hello
```

```dockerfile
CMD ["java", "Hello"]
```

### ENTRYPOINT
`ENTRYPOINT` is similar to `CMD`, however the sub-commands following `docker run` are treated as arguments to `ENTRYPOINT` instead of overriding as it was the case for `CMD`. `ENTRYPOINT` can also be overridden by `docker run --entrypoint <sub-command>`.

`ENTRYPOINT` command has two types of syntax:

#### Shell Type
`ENTRYPOINT <command>` is the shell command to be executed. This type is executed using `/bin/sh -c`.

#### JSON Array
`ENTRYPOINT ["<exec", "<arg-1>", ..., "<arg-n>"]` where `exec` is the executable to run followed by any number of arguments.