---
layout: default
title:  "Docker"
---

# Docker
{:.no_toc}

* TOC
{:toc}

## Hello World

1. Pull an image from Docker Hub.
1. Run the image in a container.
  - `--rm` is for: Remove container on exit.

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