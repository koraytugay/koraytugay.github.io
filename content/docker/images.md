---
layout: default
title:  "Docker Images"
---

# Images
{:.no_toc}

* TOC
{:toc}

## Overview
A Docker Image is a collection of all the required files for an executable. Docker Images are layered, i.e. are stacked on top of each other, with the [base image](https://hub.docker.com/search?category=base&source=verified&type=image) having no parent images.

Docker images can be downloaded from registires, [Docker Hub](https://hub.docker.com) being an example, built from `Dockerfile`s or from existing containers.

## Building an Image from a Dockerfile
Given a Dockerfile:

```dockerfile
FROM ubuntu
CMD echo HelloWorld
```

an image can be built by:

```bash
docker build -t ubuntu-helloworld .
```

## Downlading an Image From a Registry

```bash
docker pull ubuntu
```

## Creating an Image from a Container
A container can be commited using the `commit` command in a desired state to be stored as an image.

```bash
docker run -it --rm ubuntu

# Run the following inside the container
apt-get update
apt-get install -y wget

# Stop the container by exiting
exit 

docker commit container-id koraytugay/ubuntu_wget
# At this point you should have a new image you can start anytime you like
# that comes with wget

docker run -it --rm koraytugay/ubuntu
```

## Related Commands
```bash
# Pulling an image
docker pull repository-name:tag
# Examples
docker image pull mongo:3.3.11
docker pull hello-world # equivalent to :latest

# Listing images
docker image ls

# Spinning off a container from an image
docker run image-name
## Parameters
-P     # Expose ports randomly
-p <host-port>:<container:-port> # Bind ports
-d     # Run in detached mode
-i     # Make the container interactive by grabbing the standard in
-t     # Attach a terminal to the container
-rm    # Remove container upon stop
--name # Give container a name

# Removing an image
docker image rm image-id
```
