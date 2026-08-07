---
title: "Docker extra oefeningen"
weight: 4
author: Arne Duyver
draft: false
---

1. Containers met een GUI: zoek [de juiste image](https://hub.docker.com/r/kasmweb/ubuntu-bionic-desktop)
  - je kan de volgende image gebruiken: `ubuntu-bionic-desktop:1.10.0-rolling`
2. Containers voor beveiliging: [draai een webbrowser binnen een container](https://hub.docker.com/r/kasmweb/firefox)
3. Draai applicaties binnen een container: [libreoffice](https://hub.docker.com/r/kasmweb/libre-office)
4. Maak een buildfile om een commandline game in een container te draaien - [bastet](https://itsfoss.com/best-command-line-games-linux/): Een tetris versie die je in de terminal kan spelen
  - start het spel met het volgende commando: `$ /usr/games/bastet` 
5. Gebruik een GUI docker image en voeg de installatie van Java en IntelliJ toe
6. (Maak een buildfile om je FSWEB website te draaien: in Sveltekit en Laravel)

<!-- 

1. What you actually need for X11 from inside a container

For any container to open a GUI on your Windows host you need:

X server on Windows
You already have this: VcXsrv started via xserver.ps1 on display :0.

Container can reach the host
With Docker Desktop on Windows, the hostname host.docker.internal points to the Windows host.

DISPLAY in the container pointing at the host’s X server
That’s the key environment variable:

DISPLAY=host.docker.internal:0.0


(:0 display, 0 screen → :0.0)

(Optional but often helpful)

QT_X11_NO_MITSHM=1 for Qt apps (avoids some shared memory issues).

That’s it. No special volume mounts, no --net=host on Windows.

2. Where did we set that for ROS 2?

Not in the Dockerfile, but in docker-compose.yml + .env:

.env

DISPLAY=host.docker.internal:0.0


docker-compose.yml

services:
  ros2:
    build: .
    container_name: ros2-humble
    environment:
      - DISPLAY=${DISPLAY}
      - QT_X11_NO_MITSHM=1
    # ...


So the ROS 2 image itself is just a normal Linux desktop-ish environment (with Qt, OGRE, etc.);
the X11 wiring is done by docker-compose.

3. Reusing X11 in other containers

For any other GUI-capable image, you can copy the same pattern.

Example: generic Python GUI container:

version: "3.8"

services:
  my-gui-app:
    image: python:3.11
    container_name: my-gui-app
    environment:
      - DISPLAY=${DISPLAY}
    command: ["bash", "-c", "apt-get update && apt-get install -y x11-apps && xclock"]
    tty: true
    stdin_open: true


With the same .env:

DISPLAY=host.docker.internal:0.0


Start VcXsrv (.\xserver.ps1 start), then:

docker compose up


You should see xclock appear on Windows.

4. Do images need something special installed?

Yes, but it’s not Docker/X11-specific, just “does your app have GUI libs?”

ROS 2 osrf/ros:humble-desktop → already has Qt, RViz2, turtlesim, etc.

Bare images like ubuntu, python, debian → you must install whatever GUI toolkits you want (e.g. x11-apps, python3-tk, Qt, GTK, etc.).

So:

Dockerfile: install GUI libs/apps you want (rviz2, turtlesim, xclock, whatever).

Compose / docker run: set DISPLAY to point at host’s X server.

5. Using it without docker-compose (pure docker run)

If you ever want to do it directly:

docker run -it --rm `
  -e DISPLAY=host.docker.internal:0.0 `
  --name my-gui-test `
  ubuntu:22.04 bash


Inside:

apt-get update
apt-get install -y x11-apps
xclock


Again, the key bit is -e DISPLAY=host.docker.internal:0.0.

TL;DR

ROS images: already have GUI libs, that’s why RViz2/turtlesim work.

X11 over Docker on Windows: handled by DISPLAY=host.docker.internal:0.0 + VcXsrv.

To reuse in other containers:
👉 copy those environment lines (or -e DISPLAY=...) and make sure the image actually has a GUI app installed.

If you show me another Dockerfile/service you want to X-enable, I can annotate it with the minimal changes needed.

 -->