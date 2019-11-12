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
The default `bridge` network is very simple and useful for development purposes. In this mode, all containers and the host can talk to one another however there is no connection between containers on different bridge networks.

Start a busybox container and inspect it:

```bash
# Map port 5000 on host to port 6000 on busybox
docker run -it -p 5000:6000 --rm --name=my-bb busybox
docker inspect my-bb
```

With some noise removed, portion of the returned information will be as follows:

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

### Host to Container Connection
When `busybox` is listening on port `6000` with `nc -l -p 6000`, host can connect to the container by `telnet localhost 5000`. Using the containers IP address, `telnet 172.17.0.2 5000` does __not__ work. 

### Container to Host Connection
The container can connect to the host by using the the hosts IP address. Assume the host has the local IP address `192.168.0.10` and is listening on port `8000` with `nc -l -p 8000`. In this case container can connect to the host by `telnet 192.168.0.10 8000`. 

### Container to Container Connection
If we spin up yet another `busybox` container, the two containers can communicate with each other by directly using the IP addresses. Given the second container has the IP address: `172.17.0.3` and listening on port `7000` with: `nc -l -p 7000`, the first container can simply connect with `telnet 172.17.0.3 7000`.

## Communication Between Containers via Name Resolution
When containers are deployed to same network, except the default bridge network, they can communicate with each other using the container names. 

### Python - Redis Container Communication Example
Given the following simple python file:

```python
import time
import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    return cache.incr('hits')

@app.route('/')
def hello():
    count = get_hit_count()
    return "Visit count: {}".format(count)

if __name__ == "__main__":
    app.run(host="0.0.0.0", debug=True)
```

and having `flask` as the only dependency in `requirements.txt` and the following Dockerfile:

```dockerfile
FROM python:3.4-alpine
ADD . /code
WORKDIR /code
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

Build an image named `a-python-app` with 

```bash
docker build -t a-python-app .
```

Now, if we start a `redis` image and this python application as follows..

```bash
docker run --rm -d --name=redis redis
docker run --rm -d -p 5000:5000 --name=a-python-app a-python-app
```

..when we visit `localhost:5000`, we will see an error saying:

```plaintext
connecting to redis:6379. Name does not resolve.
```

For Docker containers to be able to resolve each other with name, they need to be on a network defined by us. Creating a network with `docker network create my-network` and starting the images with the following commands will allow `a-python-app` to be able to connect to the redis container using the containers name as the host name:

```bash
docker run --rm -d --name=redis --network=my-network redis
docker run --rm -d -p 5000:5000 \
  --name=a-python-app --network=my-network a-python-app
```

Visiting `localhost:5000` should now print the visit counts properly.

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