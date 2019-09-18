---
layout: default
title:  "Docker"
---

# Docker
{:.no_toc}

* TOC
{:toc}

## Hello World

1. Pull the image from Docker Hub using `docker pull hello-world`
1. Run the image using `docker run --rm hello-world`
  - `--rm` is for removing container on exit

```bash
docker pull hello-world
# Using default tag: latest
# latest: Pulling from library/hello-world

docker run --rm hello-world
# Hello from Docker!
# This message shows that your installation appears to be working correctly.
# 
# To try something more ambitious, you can run an Ubuntu container with:
#  $ docker run -it ubuntu bash
```

## Images
A Docker Image is a collection of all the required files for an executable. Docker Images are layered, i.e. are stacked on top of each other, with the [base image](https://hub.docker.com/search?category=base&source=verified&type=image) having no parent images.

### Useful Commands
```bash
# Pulling an image
docker pull repository-name/image-name:tag
# Example: docker pull library/hello-world:latest
# For official images with default latest: docker pull hello-world

# Listing images
docker image ls

# Spinning off a container from an image
docker run image-name
## Parameters
-P     # Expose ports randomly
-d     # Run in detached mode
-i     # Make the container interactive by grabbing the standard in
-t     # Attach a terminal to the container
-rm    # Remove container upon stop
--name # Give container a name

# Removing an image
docker image rm image-id
```

## Containers
A Docker Container is a running instance of an image.

### Container Lifecycle
Containers spin off from images either by `run`ing an image using `docker run image-name` or `create`ed using `docker create image-name`, the difference being a created container will not start running right away.  

An existing container can be `start`ed, `stop`ped or `restart`ed, depending on its state.

A stopped container will keep its state, the data generated in it, but the idea of containers are to be stateless and containers should ideally be started by `--rm` so that they are removed once they finish running.

### Creating an Image from a Container
A container can be `commit`ed in a desired state to be stored as an image.

__Example__
```bash
docker run -it --rm ubuntu

# Run the following inside the container
apt-get update
apt-get install -y wget
wget www.example.com

# Stop the container by exiting
exit 

docker commit container-id koraytugay/ubuntu_wget
# At this point you should have a new image you can start anytime you like
# that comes with wget

docker run -it --rm koraytugay/ubuntu
```

### Useful Commands

```bash
# List running containers
docker container ls

# List all containers
docker container ls -a

# Print exposed ports for a container
docker container port container-id

# Stop a container 
docker stop container-id

# Restart a container
docker start container-id

# Attach to a running container
docker attach container-id

# Remove a single container
docker container rm container-id

# Remove all stopped containers
docker container prune
```

## Dockerfile
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

### Dockerfile Example
Given a Dockerfile with following contents:

```dockerfile
FROM busybox
CMD echo Hello world!
```

an image can be built by 

```bash
docker build -t koraytugay/busyhello .
````

### Dockerfile Instructions
#### FROM
`FROM` is the first valid instruction of a Dockerfile and sets the base image for the build process. The syntax is: `FROM <image>[:<tag>|@digest]`. Value `latest` is the default if no `tag` or `digest` is supplied. `FROM centos` and `FROM ubuntu:16.04` are examples for the `FROM` instruction.

Using multiple `FROM` instructions is possible but discouraged.

#### COPY
`COPY <src> ... <dst>` can be used to copy multiple files (or directories) from the Docker host environment to the image, as in `COPY html /var/www/html` or `COPY file1.txt file2.txt /`.

__Example__
The following Dockerfile will copy itself into the built image, and will print the contents when the image is run.

```dockerfile
FROM busybox
COPY Dockerfile /
CMD cat Dockerfile
```

#### ADD
Please see [this](https://stackoverflow.com/questions/24958140) question on Stackoverflow for the differences between `COPY` and `ADD`.

#### ENV
`ENV <key> <value>` sets environmental variables in the image. 

```Dockerfile
ENV APACHE_LOG_DIR /var/log/apache
```

Declared values declared by `ENV` can also be used together with other instructions.

```Dockerfile
ENV BACKUP_DIR backupdir
WORKDIR /usr/${BACKUP_DIR}
```

#### ARG
`ARG <variable> [=<default value>]` lets defining variables that can be passed during Docker image build time. 

Given the following Dockerfile:

```Dockerfile
ARG user=john_doe
```

an image can be built overriding the value `johndoe` by:

```bash
docker build --build-arg user=koraytugay ,
```

Just like `ENV`, declarations can be used together with other instructions.

#### WORKDIR
`WORKDIR` changes the current working directory from `/` to path specified. This will affect `RUN` and `CMD` instructions. The path can be relative or absolute. In case it is relative, it is relative to the last `WORKDIR` set. If the directory does not exist, it will be created.

```Dockerfile
WORKDIR /mycustom/workdir
```

#### VOLUME 
todo

#### EXPOSE
`EXPOSE <port> [<port>...]` lets you expose multiple ports from the container.

#### LABEL
Metadata can be added to images using `LABEL`.

```Dockerfile
LABEL version="2.0"
      release-date="2016-08-05"
```

#### RUN
`RUN <command>` is executed when an image is being built. There can be multiple `RUN` commands in a Dockerfile, and each adds an additional layer to the image. `RUN` can also added in the form of json array syntax: `RUN ["<exec>", "<arg-1>", "<arg-n>"]`.

## Examples
### nginx on a Random Port

```bash
docker run -d -P nginx # Run in detached mode, expose ports randomly

# Find the exposed port
docker container port f53
# 80/tcp -> 0.0.0.0:32772

# Connect using telnet
telnet localhost 32772
# Trying ::1...
# Connected to localhost.
# Escape character is '^]'.
GET / HTTP/1.1
HOST: localhost

# HTTP/1.1 200 OK
# Server: nginx/1.17.3
# Date: Mon, 16 Sep 2019 19:20:44 GMT
# Content-Type: text/html
# Content-Length: 612
# Last-Modified: Tue, 13 Aug 2019 08:50:00 GMT
# Connection: keep-alive
# ETag: "5d5279b8-264"
# Accept-Ranges: bytes
# 
# <!DOCTYPE html>
# <html>
# <head>
# <title>Welcome to nginx!</title>
# .. lot more
```

### Ubuntu

```bash
docker run -it --rm ubuntu:16.04
root@4c7e285cba32:/#
```

`docker run -it --rm ubuntu:16.04 /bin/bash` also works and the why is explained [here](https://askubuntu.com/a/938872).

#### Detaching
The sequence of `CTRL+P` followed by `CTRL+Q` will detach the user from the running container. Ubuntu will still be running and the container will continue to run.

#### Attaching
Attaching back to the container can be accomplished by `docker attach container-id`.

#### Running Ubuntu in Background
Trying to run ubuntu with `docker run -d ubuntu` hoping the container will not immediately stop will not work. The correct way to achieve this is explained [here](https://stackoverflow.com/a/36872226/1173112).

## References
- [container commands](https://docs.docker.com/engine/reference/commandline/container/)
- [image commands](https://docs.docker.com/engine/reference/commandline/image/)

## Terminology
__Detached Container__: A detached container runs in the background, without being attached to any input or output stream. Running detached containers are very useful for _deamon_ programs. A daemon usually interacts with other programs or human over a network. 