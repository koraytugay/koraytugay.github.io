---
layout: default
title:  "Managing Storage"
---

# Managing Storage
{:.no_toc}

* TOC
{:toc}

## Overview
Docker containers can have storage mounted to them, either in-memory storages, from the host file system or from docker volumes. All three types of mount points can be created using the `--mount` flag on the `docker run` and `docker create` subcommands.

## Bind Mounts
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

### Using the `-v` flag for Bind Mounts
The same behaviour can be achieved using the `-v` flag:

```bash
docker run -it --rm -v /Users/kt/my-docker:/my-docker busybox
```

Using the `-v` flag is discourged by Docker for bind mounts in favor of using the more verbose `--mount` synax.

## Volumes
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

### Automatic Creation of Volumes
Creation of a volume is not required before using it. If the volume does not already exist, it will be created when a volume mapping is done. The following example uses this approach

### Serving an Application from a Tomcat Container using Volumes
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

### Anonymous Volumes
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

### Cleaning Up Volumes
Anonymous volumes are automatically deleted when the container they were created for are automatically cleaned up. Unlike anonymous volumes, named volumes must always be deleted manually. The following commands come to help when removing volumes:

```bash
docker volume rm my-volume
docker volume prune
```

### Using the VOLUME Instruction in Dockerfile
The `VOLUME` instruction does not do much by itself, and it is questionable how useful it is. See [this](https://stackoverflow.com/a/49620544), [this](https://stackoverflow.com/a/46992367/1173112) and [this](https://stackoverflow.com/a/58248523) answers for further information.

## Volume vs Mount
`-v` will create a directory on the host if it does not exist, where as `--mount` will generate an error if the directory being mounted does not already exist.