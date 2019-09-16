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

## Examples
### Running nginx on a Random Port

```bash
docker run -d -P nginx. # Run in detached mode, expose ports random

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
docker run {image-name} # REPOSITORY column

# Running an image - assigning a random port
docker run -P {image-name}

# Removing an image
docker docker image rm {image-id}
```

### Containers

```bash
# Listing containers
docker container ls -a

# Printing exposed ports for a container
docker container port {container-id}

# Removing a single container
docker container rm {container-id}

# Removing all stopped containers
docker container prune
```