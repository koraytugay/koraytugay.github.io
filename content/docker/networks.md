---
layout: default
title:  "Docker Networks"
---

# Networks
{:.no_toc}

* TOC
{:toc}

## Overview
Docker Enginer by default creates the networks `bridge`, `host` and `none`. When Docker spins up a new container, by default the container is attached to the `bridge` network. 

`host` mode only works on Linux and the container shares the hosts IP address. A container running in `none` network cannot communicate with the external word. 

## Bridge Network
Bridge network is very simple and useful for development purposes.

## Related Commands

```bash
docker network create

docker network connect

docker network disconnect

docker network inspect

docker network ls

docker network rm
```

## Random Notes
- Container to host: `telnet host.docker.internal <port-number>`