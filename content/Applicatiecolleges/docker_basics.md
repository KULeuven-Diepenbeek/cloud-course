---
title: "Docker Basics"
weight: 1
author: Arne Duyver
draft: true
---

### Demo 1: Hello World

```bash
$ docker run hello-world
```
Merk op dat de container automatisch stopt.

### Demo 2: Static Website
<!-- TODO: -->

#### Binding ports and detached mode

#### Interact with container

#### Environment variables

#### Attach terminal to a container

#### Docker persistance and docker volumes

### Exercise 1: 
- Gebruik de nginx base image en maak gebruik van docker volumes om je eigen statische website te tonen

### Exercise 2: 
- Gebruik de mongodb image en maak een locale database aan 
- Interageer met de database door middel van een attached console en de commandline interface van de database
- Interageer met de database door middel van een rest client zoals thunderclient via get en post requests
<!-- TODO: DO this myself -->

### Exercise 3:
- Breidt de functionaliteit van je website uit [Exercise 1](#exercise-1) uit zodat deze via javascript calls maakt naar de database om data uit de database te displayen. (Je mag ht ip-adres, de port, username, password en databasenaam hardcoded opslaan in de javascript)
<!-- TODO: DO this myself En put solutions here in commment so you can uncomment in lesson -->

### Demo 3: Docker compose
<!-- TODO: put everything from demo 2 in a docker-compose.yml -->

### Demo 4: Maak eigen docker images via docker build files
<!-- TODO: recreate the image using the nginx base image and docker build (give more info on the pull command and how to erase images) -->

### Exercise 4: 
- Neem je website uit [Exercise 1](#exercise-1) (of [Exercise 3](#exercise-3)) en laat de website identiek werken, maar nu door enkel gebruik te maken van een docker build file.
- Zet alle configuraties ook in een docker-compose file die je build file als image gebruikt.
<!-- TODO: DO this myself -->