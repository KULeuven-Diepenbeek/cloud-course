---
title: "Applicatiecolleges"
weight: 1
author: Arne Duyver
draft: false
---

## Docker

We gaan uiteindelijk in de applicatiecolleges verschillende api's aanmaken. Belangrijk hierbij is dat die microservices gemakkelijk deployable zijn in de cloud. Om dit zo vlekkeloos mogelijk te laten verlopen gaan we gebruik maken van docker containers. Docker containers zijn ook zeer handig om developer omgevingen op te zetten zoals virtual environments in python, maar dan nog meer flexibel.

### Installing docker

Volg de instructies op deze [webpagina](https://docs.docker.com/engine/install/) om docker correct te installeren op jouw pc.

**Enable zeker de Virtualisatie optie in je BIOS. Zie [hier](https://www.virtualmetric.com/blog/how-to-enable-hardware-virtualization) hoe je dit doet. _via de Task Manager (Ctrl+Shift+Esc) -> performance tab -> cpu kan je ook kijken of 'Virtualization' enabled is_**

(Windows gebruikers kunnen Docker desktop gebruiken om te interageren met containers, maar we gaan in deze cursus vooral gebruik maken van de terminal)

### Docker images en build files
Al dan niet goed gedocumenteerd:
- [Dockerhub](https://hub.docker.com/)
- [Github](https://www.github.com)

### VSCode

Als code/text editor gaan we gebruik maken van VSCode omdat je hier een aantal extensies kan voor installeren waarmee je zeer gemakkelijk kan werken aan/in docker containers.

Het gaat over volgende extensies:
- Docker
- (WSL)