---
layout: default
title:  "Services"
---

# Services
{:.no_toc}

* TOC
{:toc}

## Overview
Unlike containers, Docker services are available only when Docker is running in swarm mode. Initializing swarm mode starts an internal database and a long-running loop in the Docker Engine that performs service orchestration. 

## Swarm
The Swarm orchestration system is a state reconciliation loop that continuously compares the declared state of the system that the user desires with the current state of the system. When it detects a difference, it uses a simple set of rules to change the system so that it matches the desired state.

To start Tomcat in a service instead of a single container:

```bash
docker swarm init # If not already initialized
docker service create -p 8080:8080 --name my-tomcat tomcat:8
```

Tomcat will be available in  `localhost:8080` and `docker container ls` will return the running container information:

```plaintext
ID  IMAGE     COMMAND            PORTS     NAMES
e7  tomcat:8  "catalina.sh run"  8080/tcp  my-tomcat.1.s4qs
```

If this container is forced to be removed (`docker container rm e7 -f`) when it is running inside the service, it will be brought back to life. This is one of the properties of running in service mode, compared to the traditional container spin up.
