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
Containers spin off from images either by runing an image using `docker run image-name` or created using `docker create image-name`, the difference being a created container will not start running right away. An existing container can be started, stopped or restarted, depending on its state.

A stopped container will keep its state, the data generated in it, but the idea of containers are to be stateless and containers should ideally be started by `--rm` so that they are removed once they finish running.

## Related Commands

```bash
# List running containers
docker container ls

# List all containers
docker container ls -a

# Stop a container 
docker stop <container-id>

# Inspect a container
docker inspect <container-id>

# Find port bindings
docker port <container-id>

# Spin off a container from an image
docker run image-name
## Parameters
-P     # Bind all exposed ports to random ports
-p <host-port>:<container-port> # Bind host port to container port
-p <container-port> # Bind port to a random port on host
-d     # Run in detached mode
-i     # Make the container interactive by grabbing the standard in
-t     # Attach a terminal to the container
-v <volume-name>:<container-dir> # Bind volume
-v <host-absolute-path>:<container-dir> # Bind host directory to container
-rm    # Remove container upon stop
--name # Give container a name, --name=my-container

# Restart a container
docker start <container-id>

# Attach to a running container
docker attach <container-id>

# Remove a single container
docker container rm <container-id>

# Remove all stopped containers
docker container prune
```