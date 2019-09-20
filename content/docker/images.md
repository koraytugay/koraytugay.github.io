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

Docker images can be downloaded from the [Docker Hub](https://hub.docker.com) or built from `Dockerfile`s. 

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
