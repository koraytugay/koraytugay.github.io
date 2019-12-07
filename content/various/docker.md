# Docker

---
layout: default
title:  "Docker"
---

# Docker
{:.no_toc}

* TOC
{:toc}

## Images
A Docker Image is a collection of all the required files for an executable. Docker Images are layered with the base image having no parent images. Docker images can be downloaded from registires, Docker Hub being an example, built from Dockerfiles or from existing containers.

### Related Commands
```bash
# Pulling an image
docker pull repository-name:tag
# Examples
docker image pull mongo:3.3.11
docker pull hello-world # equivalent to :latest

# Listing images
docker image ls

# Filtering images
docker image ls | grep tomcat

# Removing an image
docker image rm image-id
```

## Containers
Containers spin off from images either by runing an image using `docker run image-name` or created using `docker create image-name`, the difference being a created container will not start running right away. An existing container can be started, stopped or restarted, depending on its state.

A stopped container will keep its state, the data generated in it, but the idea of containers are to be stateless and containers should ideally be started by `--rm` so that they are removed once they finish running.

### Related Commands
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

# Start a stopped container
docker start <container-id>

# Attach to a running container
docker attach <container-id>

# Remove a single container
docker container rm <container-id>

# Remove all stopped containers
docker container prune
```

### Creating an Image from a Container
A container can be commited using the commit command in a desired state to be stored as an image.

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

## Dockerfile
A [Dockerfile](https://docs.docker.com/v17.12/engine/reference/builder/) is a text-based build script from which images can be built. The instructions of Dockerfile can contain:

- Selecting the base image
- Installing the required application
- Adding the configuration and data files
- Commands to running services
- Commands to expose the services to outside

A Dockerfile is in the form of

```dockerfile
# This is a comment.
INSTRUCTION argument(s)
```

where instructions are case-insensitive but best practice is to have them uppercase. Given a Dockerfile with following contents:

```dockerfile
FROM busybox
CMD echo Hello world!
```

an image can be built by:

```bash
docker build -t koraytugay/busyhello .
````

Another advantage of using Dockerfiles is, they integrate easily with existing build systems and continuous integration tools.

See the [official docs](https://docs.docker.com/engine/reference/builder/) for detailed information on Dockerfile instructions.

## Storage
Docker containers can have storage mounted to them, either in-memory storages, from the host file system or from docker volumes. All three types of mount points can be created using the `--mount` flag on the `docker run` and `docker create` subcommands.

### Bind Mounts
This approach mounts a directory in the host filesystem to the container. Bind mounts are useful in development environments when the container creates log files, needs to operate on a file on the host system or needs to produce a file that will be stored on the host system after container stops. However this approach has its own problems such as depending on a specific location on the host container the container is running. They also create an opportunity for conflict with other containers.

The following will start a busybox container where `/Users/kt/my-docker` is mounted to `/my-docker` in the running container. Paths must be absolute. You can use `$PWD` to start from the working path, as in `$PWD/my-docker`. 

```bash
docker run \
  --rm -it \
  --mount type=bind,source=/Users/kt/my-docker,target=/my-docker \
  busybox
```

Directories can also be mounted with readonly by providing `readonly=true` as follows:

```bash
docker run \
  --rm -it \
  --mount type=bind,source=/Users/kt/my-docker,target=/my-docker,readonly=true \
  busybox
```

#### Using the `-v` flag for Bind Mounts
The same behaviour can be achieved using the `-v` flag:

```bash
docker run -it --rm -v /Users/kt/my-docker:/my-docker busybox
```

Using the `-v` flag is discourged by Docker for bind mounts in favor of using the more verbose `--mount` synax.

### Volumes
Docker volumes are named filesystem trees managed by Docker. They can be implemented with disk storage on the host filesystem, or more exotic solutions such as cloud storage. All operations on Docker volumes can be accomplished using the docker `volume` subcommand set. 

Using volumes is a method of decoupling storage from specialized locations on the filesystem that one might specify with bind mounts.

Using Docker volumes is a way of sayinig

> I need a place to put some data that I’m working with. 

This is a requirement that Docker can fill on any machine with Docker installed, which means containers are de-coupled from the host filesystem, in contrast to bind mounts.

A volume can be created as follows: 

```bash
docker volume create my-volume
```

And the created volume can be inspected as follows:

```bash
docker volume inspect my-volume
```

which will return a result akin to:

```json
[
    {
        "CreatedAt": "2019-11-09T19:14:19Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/my-volume/_data",
        "Name": "my-volume",
        "Options": {},
        "Scope": "local"
    }
]
```

To make use of the volume we created, lets spin up a `busybox` container that will be removed upon completion, attaching the `data` directory to this volume and lets create a file in the `data` folder and stop the container:

```bash
docker run -it --rm -v my-volume:/data busybox
cd data
touch hello.txt
echo Hello > hello.txt
```

Spin up a new container by `docker run -it --rm -v my-volume:/data busybox` and you will be able to verify that your data is not lost.

#### Automatic Creation of Volumes
Creation of a volume is not required before using it. If the volume does not already exist, it will be created when a volume mapping is done. The following example uses this approach

#### Serving an Application from a Tomcat Container using Volumes
Imagine we have a `sample.war` file we want to deploy to Tomcat, and we want our application to live on a volume and Tomcat to run in a container. Here is a possible approach to achieve this. 

Start a `busybox` container as follows, where a volume named `mywar` is mapped to the `/war` folder in the container. Also a bind mount mapping is done from the host system so we can copy the `sample.war` file to `/war` directory, hence to the `mywar` volume.

```bash
docker run --rm -it -v /Users/kt/warfile:/warfile -v mywar:/war busybox
cp /warfile/sample.war /war
```

Now that we have our war file in `mywar` volume, start Tomcat:

```bash
docker run \
  -p 8080:8080 \
  -it --rm \
  -v mywar:/usr/local/tomcat/webapps \
  tomcat:7-jdk8
```

What is important in this example is, we are overriding a folder in the Tomcat container with a volume. Application will be available at `localhost:8080/sample`.

#### Anonymous Volumes
If different containers use volumes with same names, a volume conflict and unwanted behaviour will be witnessed. This conflict can be solved by using anonymous volumes. When a volume is created without a specific name, a volume will be created with a unique id.

```bash
docker volume create
# 98a5edfe209b4a1c04e2a768f2db50263294e0c49862d98b0bf1e4c8ea970c1a
```

Anonymous volumes can be shared between containers using `--volumes-from`. The following starts a `busybox` container with an anonymous volume mapped to `/shared` folder within the container.

```bash
docker run --name busy-box-one -v /shared -it --rm busybox
```

For another `busybox` to share the same anonymous volume, the second container can be started as follows:

```bash
docker run --name busy-box-two --volumes-from busy-box-one -it --rm busybox
```

#### Cleaning Up Volumes
Anonymous volumes are automatically deleted when the container they were created for are automatically cleaned up. Unlike anonymous volumes, named volumes must always be deleted manually. The following commands come to help when removing volumes:

```bash
docker volume rm my-volume
docker volume prune
```

#### Using the VOLUME Instruction in Dockerfile
The `VOLUME` instruction does not do much by itself, and it is questionable how useful it is. See [this](https://stackoverflow.com/a/49620544), [this](https://stackoverflow.com/a/46992367/1173112) and [this](https://stackoverflow.com/a/58248523) answers for further information.

### Volume vs Mount
`-v` will create a directory on the host if it does not exist, where as `--mount` will generate an error if the directory being mounted does not already exist.

## Networks
Docker Enginer by default creates the networks `bridge`, `host` and `none`. When Docker spins up a new container, by default the container is attached to the `bridge` network. 

`host` mode only works on Linux and the container shares the hosts IP address. A container running in `none` network cannot communicate with the external word. 

### Bridge Network
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

#### Host to Container Connection
When `busybox` is listening on port `6000` with `nc -l -p 6000`, host can connect to the container by `telnet localhost 5000`. Using the containers IP address, `telnet 172.17.0.2 5000` does __not__ work. 

#### Container to Host Connection
The container can connect to the host by using the the hosts IP address. Assume the host has the local IP address `192.168.0.10` and is listening on port `8000` with `nc -l -p 8000`. In this case container can connect to the host by `telnet 192.168.0.10 8000`. 

#### Container to Container Connection
If we spin up yet another `busybox` container, the two containers can communicate with each other by directly using the IP addresses. Given the second container has the IP address: `172.17.0.3` and listening on port `7000` with: `nc -l -p 7000`, the first container can simply connect with `telnet 172.17.0.3 7000`.

### Communication Between Containers via Name Resolution
When containers are deployed to same network, except the default bridge network, they can communicate with each other using the container names. 

#### Python - Redis Container Communication Example
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

### Related Commands

```bash
docker network create
docker network connect
docker network disconnect
docker network inspect
docker network ls
docker network rm
```

## Docker Compose
Docker Compose is a tool for defining and running multiple containers. See [Overview of Docker Compose](https://docs.docker.com/compose/) for more details.

### Hello World Example
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

Starting containers can be achieved via `docker-compose up` (`-d` for deamon). When the containers above is running, `docker-compose images` will print something akin to:

```plaintext
             Container                 Repository    Tag     Id      Size 
--------------------------------------------------------------------------
my-docker-compose_back-end-server_1    nginx        latest   6d63   120 MB
my-docker-compose_front-end-server_1   nginx        latest   6d63   120 MB
```

`docker-compose stop` will stop multiple containers, and `docker-compose down` will stop and remove all the containers.

### Docker Compose and Network
Containers started with Docker Compose will be on the same network, and it will not be the default network. A network by Docker Compose will be created when `docker-compose up` is called. This means, containers started with Docker Compose will be able to talk to each other via container names, since container names will be resolved as host names.

For further information, see [Networking in Compose](https://docs.docker.com/compose/networking/).

## Examples
### Hello World

1. Pull the image from Docker Hub using `docker pull hello-world`
1. Run the image using `docker run --rm hello-world`
  - `--rm` is for removing container on exit

```bash
docker pull hello-world
# Using default tag: latest
# latest: Pulling from library/hello-world

docker run --rm hello-world
# Hello from Docker!
# This message shows that your installation appears to be working correctly.
# 
# To try something more ambitious, you can run an Ubuntu container with:
#  $ docker run -it ubuntu bash
```

### Java Hello World
This example demonstrates compiling the source file in image creation time and executing the java program in container launch. 

```java
class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello World");
    }
}
```

```java
class Goodbye {
    public static void main(String[] args) {
        System.out.println("Goodbye");
    }
}
```

```dockerfile
FROM openjdk:8
WORKDIR /helloworld
COPY HelloWorld.java .
COPY Goodbye.java .
RUN javac HelloWorld.java
RUN javac Goodbye.java
CMD java HelloWorld
```

__Execution__

```bash
docker build -t java-helloworld .  # Build the image
docker run --rm java-helloworld    # Run the container
```

`CMD java HelloWorld` can be overridden in container launch time as follows:

```bash
docker build -t java-helloworld .              # Build the image
docker run --rm java-helloworld java Goodbye   # Override CMD
```

### nginx on a Random Port

```bash
docker run -d -P nginx # Run in detached mode, expose ports randomly

# Find the exposed port
docker container port f53
# 80/tcp -> 0.0.0.0:32772

# Connect using telnet
telnet localhost 32772
# Trying ::1...
# Connected to localhost.
# Escape character is '^]'.
GET / HTTP/1.1
HOST: localhost

# HTTP/1.1 200 OK
# 
# <!DOCTYPE html>
# <html>
# <head>
# <title>Welcome to nginx!</title>
# ..
```

### Ubuntu

```bash
docker run -it --rm ubuntu:16.04
root@4c7e285cba32:/#
```

`docker run -it --rm ubuntu:16.04 /bin/bash` also works and the why is explained [here](https://askubuntu.com/a/938872).

#### Detaching
The sequence of `CTRL+P` followed by `CTRL+Q` will detach the user from the running container. Ubuntu will still be running and the container will continue to run.

#### Attaching
Attaching back to the container can be accomplished by `docker attach container-id`.

#### Running Ubuntu in Background
Trying to run ubuntu with `docker run -d ubuntu` hoping the container will not immediately stop will not work. The correct way to achieve this is explained [here](https://stackoverflow.com/a/36872226/1173112).

### Testing Java Applications Against Various JREs in Containers
Java is supposed to be _compile once, run anywhere_ but sometimes it is just not the case and you might end up needing to run your application against a specific JRE. This can easily be achieved by using Docker.

1. Download the JRE version you want to verify your application against from Oracle 's [download](https://www.oracle.com/technetwork/java/javase/downloads/java-archive-javase8-2177648.html) page to a folder you will share with the container. For me it will be `/app`. Stick to a 64-bit linux tar option.
1. Put your jar file to `/app` as well.
1. Start ubuntu in a container with `docker run --rm -it -v /app:/app -v ubuntu:16.04`.
1. Inside the container go to `/usr/java`, create it if it does not exist.
1. Copy the tar file you downloaded with `cp /app/jre-xxx-linux-x64.tar.gz .`.
1. Extract it `tar zxvf jre-8u45-linux-x64.tar.gz`.
1. Fix the version and execute `export JAVA_HOME=/usr/java/jre1.8.0_45`.
1. Execute `export PATH=${PATH}:${JAVA_HOME}/bin`.
1. Navigate to `app` via `cd /app` and execute your program with `java ..`.

### Using ffmpeg in a Container

```bash
docker run --rm \
    --mount type=bind,source=$(PWD),target=/tmp 
    jrottenberg/ffmpeg -i /tmp/a.mov /tmp/a.mp4
```

### Using Git in a Container
Build an image that will allow us to run git making use of the following Dockerfile by running the command: `docker image build -t alpine-git .`.

```dockerfile
FROM alpine:3.10
RUN apk add --no-cache git
```

Cloning and updating a git repository using the container:

```bash
docker container run --rm -w /tmp -v \
    $PWD:/tmp alpine-git git clone https://github.com/koraytugay/glw.git
docker container run --rm -w /tmp/glw -v \
    $PWD:/tmp alpine-git git pull
```

Instead of changing the working directory, multiple commands can be used as follows:

```bash
docker container run --rm -v $PWD:/tmp alpine-git /bin/sh -c 
    \ "cd tmp; git clone https://github.com/koraytugay/glw.git"
docker container run --rm -v $PWD:/tmp alpine-git /bin/sh -c 
    \ "cd tmp/glw; git pull"
```

## Random Notes
### Operating System of a Running Container
`more /etc/os-release` can be used to print what operating system the container is using. For example, for gcc:

```properties
PRETTY_NAME="Debian GNU/Linux 10 (buster)"
NAME="Debian GNU/Linux"
VERSION_ID="10"
VERSION="10 (buster)"
VERSION_CODENAME=buster
ID=debian
HOME_URL="https://www.debian.org/"
SUPPORT_URL="https://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"
```

### Container to Host Connection
`host.docker.internal <port-number>` can be used to connect to the host machine within a container.