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
This approach mounts a directory in the host filesystem to the container. Bind mounts are useful in development environments when the container creates log files, needs to operate on a file on the host system or needs to produce a file that will be stored on the host system after container stops. However but this approach has its own problems such as depending on a specific location on the host container is running. They also create an opportunity for conflict with other containers.

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
Start by creating a volume:

```bash
docker volume create data-volume
```

Spin up a `busybox` container that will be removed upon completion, attaching the `data` directory to the volume you just created. Create a file in the `data` folder and stop the container:

```bash
docker run -it --rm -v data-volume:/data busybox
cd data
touch hello.txt
echo Hello > hello.txt
exit
```

Spin up a new container by `docker run -it --rm -v data-volume:/data busybox` and you will be able to verify that your data is not lost. You can even spin up an `ubuntu` image and the `data` directory will still be accessible. 

### Using the VOLUME Instruction in Dockerfile
The `VOLUME` instruction does not do much by itself, and it is questionable how useful it is. See [this](https://stackoverflow.com/a/49620544), [this](https://stackoverflow.com/a/46992367/1173112) and [this](https://stackoverflow.com/a/58248523) answers for further information.

## Volume vs Mount
`-v` will create a directory on the host if it does not exist, where as `--mount` will generate an error if the directory being mounted does not already exist.