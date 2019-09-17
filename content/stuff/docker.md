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
# 1b930d010525: Pull complete 
# Digest: sha256:b8ba256769a0ac28dd126d584e0a2011cd2877f3f76e093a7ae560f2a5301c00
# Status: Downloaded newer image for hello-world:latest
# docker.io/library/hello-world:latest

docker run --rm hello-world
# Hello from Docker!
# This message shows that your installation appears to be working correctly.
# 
# To generate this message, Docker took the following steps:
#  1. The Docker client contacted the Docker daemon.
#  2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
#     (amd64)
#  3. The Docker daemon created a new container from that image which runs the
#     executable that produces the output you are currently reading.
#  4. The Docker daemon streamed that output to the Docker client, which sent it
#     to your terminal.
# 
# To try something more ambitious, you can run an Ubuntu container with:
#  $ docker run -it ubuntu bash
```

## Containers
### Container Lifecycle
Containers spin off from images either by `run`ing an image using `docker run {image-name}` or `create`ed using `docker create {image-name}`, the difference being a created container will not start running right away.  

An existing container can be `start`ed, `stop`ped or `restart`ed, depending on its state.

A stopped container will keep its state, the data generated in it, but the idea of containers are to be stateless and containers should ideally be started by `--rm` so that they are removed once they finish running.

### Useful Commands

```bash
# List running containers
docker container ls

# List all containers
docker container ls -a

# Print exposed ports for a container
docker container port {container-id}

# Stop a container 
docker stop {container-id}

# Restart a container
docker start {container-id}

# Attach to a running container
docker attach {container-id}

# Remove a single container
docker container rm {container-id}

# Remove all stopped containers
docker container prune
```

## Examples
### nginx on a Random Port

```bash
docker run -d -P nginx # Run in detached mode, expose ports random

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
# -i is for making the container interactive by grabbing the standard in
# -t is for attaching a terminal to the container
# -rm is for removing container upon stop
docker run -it --rm ubuntu:16.04
root@4c7e285cba32:/#
```

`docker run -it --rm ubuntu:16.04 /bin/bash` also works and the why is explained [here](https://askubuntu.com/a/938872).

#### Detaching
The sequence of `CTRL+P` followed by `CTRL+Q` will detach the user from the running container. Ubuntu will still be running and the container will continue to run.

#### Attaching
Attaching back to the container can be accomplished by `docker attach {container-id}`.

#### Running Ubuntu in Background
Trying to run ubuntu with `docker run -d ubuntu` hoping the container will not immediately stop will not work. The correct way to achieve this is explained [here](https://stackoverflow.com/a/36872226/1173112).

#### Using the Package Manager
`wget` can be installed as follows to ubuntu running within the container:

```bash
apt-get update
apt-get install -y wget
wget www.example.com
```

## Cheat Sheet
### Images

```bash
# Pulling an image
docker pull {repository-name/image-name:tag}
# Example: docker pull library/hello-world:latest
# For official images with default latest: docker pull hello-world

# Listing images
docker image ls

# Running an image - this will end up creating a new container
docker run {image-name}

# Running an image
docker run {image-name}
## Parameters
-P     # Expose ports randomly
-d     # Run in detached mode
-it    # Interactive 
--name # Give container a name

# Removing an image
docker docker image rm {image-id}
```


## References
- [container commands](https://docs.docker.com/engine/reference/commandline/container/)
- [image commands](https://docs.docker.com/engine/reference/commandline/image/)

## Terminology
__Detached Container__: A detached container runs in the background, without being attached to any input or output stream. Running detached containers are very useful for _deamon_ programs. A daemon usually interacts with other programs or humsn over a network. 