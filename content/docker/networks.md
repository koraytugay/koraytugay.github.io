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
The default `bridge` network is very simple and useful for development purposes. In this mode, all containers and the host can talk to one another however there is no connection between containrs on different bridge networks.

Start a jetty container and inspect it:

```bash
# Map port 5000 on host to port 6000 on busybox
docker run -it -p 5000:6000 --rm --name=my-bb busybox
docker inspect my-bb
```

With some noise removed, a sample information returned will be as follows:

```json
"NetworkSettings": {
    "Bridge": "",
    "Ports": {
        "6000/tcp": [
            {
                "HostIp": "0.0.0.0",
                "HostPort": "5000"
            }
        ]
    },
    "Gateway": "172.17.0.1",
    "IPAddress": "172.17.0.2",
    "IPPrefixLen": 16,
    "Networks": {
        "bridge": {
            "Gateway": "172.17.0.1",
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
          }
    }
}
```

### Container to Host Connection

Here, the hosts IP address is `192.168.0.10` which is not seen anywhere returned by `container inspect`.

When `busybox` is listening on port `6000` with `nc -l -p 6000`, host can connect to the container by `telnet localhost 5000`. Using the containers IP address, `telnet 172.17.0.2 5000` does __not__ work. 

The container, in return, can connect to the host by using the IP address. Assume the host is listening on port `8000` with `nc -l -p 8000`. In this case container can connect to the host by `telnet 192.168.0.10 8000`. 

### Container to Container Connection
If we spin up yet another `busybox` container, the two containers can communicate with each other by directly using the IP addresses. Given the second container has the IP address: `172.17.0.3` and listening on port `7000` with: `nc -l -p 7000`, the first container can simply connect with `telnet 172.17.0.3 7000`.

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