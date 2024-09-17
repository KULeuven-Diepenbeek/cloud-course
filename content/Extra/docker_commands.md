---
title: "Docker Commands"
weight: 1
author: Arne Duyver
draft: false
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

# Remove a container
$ docker rm <containername or (partof)containerid>
## You can put multiple container names after each other with spaces in between to start/stop/remove multiple containers at once

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
Simple example:
```yml
# docker-compose.yml
version: '3.8'

services:
  static_website:
    image: dockersamples/static-site:latest
    container_name: static_website
    ports:
      - "9090:80"
    environment:
      - AUTHOR=YourName
    volumes:
      - static_website_volume:/usr/share/nginx/html
    restart: always

volumes:
  static_website_volume:
```
- Zorg ervoor dat je met je terminal in de directory van je docker-compose file zit.
- Maak de container(s) aan en start ze met `$ docker-compose up -d` met de flag `-d` voor detached mode.
- Stop en remove de container(s) met `$ docker-compose down`.

<!--TODO: ### Docker build file commands-->