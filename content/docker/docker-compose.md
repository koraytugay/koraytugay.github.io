---
layout: default
title:  "Docker Compose"
---

# Docker Compose
{:.no_toc}

* TOC
{:toc}

## Overview
Docker Compose is a tool for defining and running multiple containers. Docker Compose uses `yml` (`docker-compose.yml` being the standard) files to configure services. Using Docker Compose services can be started, stopped or scaled with single commands.

When you need to model a whole environment of services, you should use a stack. A stack describes collections of services, volumes, networks, and other configurations. Stacks are created from a declarative description of an entire environment. These environments are described using the Docker Compose file format.

## Hello World Example
The following `docker-compose.yml` file can be used by Docker Compose to start multiple nginx servers:

```yml
version: "3.7"
services:
  front-end-server:
    image: nginx
    ports:
      - 8080:80
  back-end-server:
    image: nginx
    ports:
      - 8090:80
```

Starting services can be achieved via `docker-compose up` (`-d` for deamon) and stopping the services can be achieved via `docker-compose down`.

When the services above is running, `docker-compose images` will print something akin to:

```plaintext
             Container                 Repository    Tag     Id      Size 
--------------------------------------------------------------------------
my-docker-compose_back-end-server_1    nginx        latest   6d63   120 MB
my-docker-compose_front-end-server_1   nginx        latest   6d63   120 MB

```

`docker-compose stop` will stop multiple containers, and `docker-compose down` will stop and remove all the containers.

## Docker Compose and Network
Containers started with Docker Compose will be on the same network, and it will not be the default network. A network by Docker Compose will be created when `docker-compose up` is called. This means, containers started with Docker Compose will be able to talk to each other via container names, since container names will be resolved as host names.