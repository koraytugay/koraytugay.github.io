---
layout: default
title:  "Docker Volumes"
---

# Volumes
{:.no_toc}

* TOC
{:toc}

## Overview
Volumes are used for decoupling containers from storage and sharing data between containers. When volumes are _properly_ used, data is not lost upon container removal.

## Using the VOLUME Instruction in Dockerfile
The `VOLUME` instruction does not do much by itself, and it is questionable how useful it is. See [this](https://stackoverflow.com/a/49620544), [this](https://stackoverflow.com/a/46992367/1173112) and [this](https://stackoverflow.com/a/58248523) answers for further information.

## Sharing Data Between Containers With Docker Volumes
Start by creating a volume:

```bash
docker volume create data-volume
```

Spin up a `busybox` container that will be removed upon completion, attaching the `data` directory to the volume you just created. Create a file in the `data` folder and stop the container:

```bash
docker run -it --rm -v data-volume:/data busybox
cd data
touch hello.txt
echo Hello > hello.txt
exit
```

Spin up a new container by `docker run -it --rm -v data-volume:/data busybox` and you will be able to verify that your data is not lost. You can even spin up an `ubuntu` image and the `data` directory will still be accessible. 

## Attaching a Directory From Host to a Container
Mounting a local directory is very much same with attaching a volume to a container. Here is an example:

```bash
docker run -it --rm -v /Users/kt/delete-me:/data busybox
```

The host path must be an absolute path. You can use `$(pwd)` or (`"$PWD"`) to start from the working path, as in `$(pwd)/resources`. 

## Related Commands

```bash
# Create a volume
docker volume create <volume-name>

# Inspect a volume
docker volume inspect <volume-name>

# List volumes
docker volume ls

# Remove a volume
docker volume rm <volume-name>

# Prune volumes
docker volume prune
```
