---
layout: default
title:  "Docker Networks"
---

# Networks
{:.no_toc}

* TOC
{:toc}

## Single Container - Default Network
Start a busybox container named my-bb with port 5000 exposed in interactive mode:

```bash
docker run -it -p 5000:5000 --rm --name my-bb busybox
```

### Container to Host Connection
You can connect to my-bb from the host simply using `localhost` and the port exposed. In my-bb starting listening on port 5000:

```bash
nc -l -p 5000
```

For connecting to container from host:

```bash
telnet localhost 5000
```

### Host to Container Connection
Start listening on port 5001 in host:

```bash
nc -l -p 5001
```

For connecting to host from container:

```bash
telnet host.docker.internal 5001
```