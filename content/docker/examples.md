---
layout: default
title:  "Docker Examples"
---

# Examples
{:.no_toc}

* TOC
{:toc}

## Hello World

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

## Java Hello World
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

## nginx on a Random Port

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

## Ubuntu

```bash
docker run -it --rm ubuntu:16.04
root@4c7e285cba32:/#
```

`docker run -it --rm ubuntu:16.04 /bin/bash` also works and the why is explained [here](https://askubuntu.com/a/938872).

### Detaching
The sequence of `CTRL+P` followed by `CTRL+Q` will detach the user from the running container. Ubuntu will still be running and the container will continue to run.

### Attaching
Attaching back to the container can be accomplished by `docker attach container-id`.

### Running Ubuntu in Background
Trying to run ubuntu with `docker run -d ubuntu` hoping the container will not immediately stop will not work. The correct way to achieve this is explained [here](https://stackoverflow.com/a/36872226/1173112).
