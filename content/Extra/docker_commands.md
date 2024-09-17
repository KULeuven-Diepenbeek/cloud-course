---
title: "Docker Commands"
weight: 1
author: Arne Duyver
draft: true
---

### Overview of Docker Commands

```bash
# Download/pull an image
$ docker pull <imagename>

# Run a container
$ docker run <imagename>
# Run a container with specific name
$ docker run --name <containername> <imagename>
# Run a container and bind port
$ docker run -p <hostport>:<containerport> <imagename>
# Run a container in detached mode
$ docker run -d <imagename>
# Run a container with environment variables
$ docker run -e <envkey>=<envvalue> <imagename>
# Run a container and bind volume
$ docker run -v <dockervolume>:<containerdirectory_absolutepath> <imagename>
# Run a container and bind host directory
$ docker run -v <hostdirectory_absolutepath>:<containerdirectory_absolutepath> <imagename>


# Stop a container
$ docker stop <containername or (partof)containerid>

# List running docker containers
$ docker ps
# List all docker containers
$ docker ps --all
$ docker ps -a

# Create a docker volume
$ docker volume create <volumename>

# Attach terminal to running container
$ docker exec -it <containername> /bin/bash
```

### Docker-compose.yml example/template

```yaml
TODO:
```

### Docker build file commands

```
TODO:
```