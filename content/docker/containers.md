---
layout: default
title:  "Docker Containers"
---

# Containers
{:.no_toc}

* TOC
{:toc}

## Overview
A Docker Container is a running instance of an image.

## Container Lifecycle
Containers spin off from images either by `run`ing an image using `docker run image-name` or `create`ed using `docker create image-name`, the difference being a created container will not start running right away.  

An existing container can be `start`ed, `stop`ped or `restart`ed, depending on its state.

A stopped container will keep its state, the data generated in it, but the idea of containers are to be stateless and containers should ideally be started by `--rm` so that they are removed once they finish running.

## Creating an Image from a Container
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

## Related Commands

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