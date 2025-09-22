---
title: "Docker Build Files"
weight: 2
author: Arne Duyver
draft: false
---

We weten nu hoe we containers kunnen aanmaken en bewerken met bijvoorbeeld docker volumes. Soms kan het echter handig zijn om onze eigen docker images aan te maken. Dit kan met behulp van een `Dockerfile`

### Demo 1: Python Flask development omgeving. 
Het is hier ook weer vanzelfsprekend dat we het wiel niet volledig gaan heruitvinden. Daarom start onze eigen docker image steeds van een bestaande docker image, waar we aan verder bouwen. We maken eerst een file genaamd `Dockerfile` aan. Als voorbeeld gaan we een docker image maken dat gebruikt kan worden om Flask (python backend) apps te ontwikkelen. We starten hiervoor met een python base image:
```Dockerfile
FROM python:3.10
```
Daarna kunnen we definiëren wat onze "home" directory zal zijn (waar we alle commando's uitvoeren in de image en waar we starten met onze relatieve paden etc.):
```Dockerfile
# Absoluut pad /app in de container/image
WORKDIR /app
```
Ik kan instellen als welke gebruiker ik aanpassingen uitvoer (dit kan soms afhangen van welk baseimage je gebruikt):
```Dockerfile
USER root
```
Nu komt het belangrijke deel. Ik kan alle files die nodig zijn al kopiëren naar een directory in de image:
```Dockerfile
# noem eerst de directory/file van je host en daarna de directory in de image waar je ze naartoe wil kopiëren
COPY ./app .
COPY ./entrypoint.sh ../entrypoint.sh
```
Je voert de commando's uit die nog uitgevoerd moeten worden:
```Dockerfile
# De file die we steeds als commando gaan starten moet een executable zijn.
RUN chmod +x ../entrypoint.sh
```
Aangezien we een webapplicatie gaan lanceren met Flask moeten we ook nog de correcte port exposen. (Belangrijk! dit is niet hetzelfde als ze koppelen aan de host. Dat moeten we nog definiëren wanneer we effectief een container gaan aanmaken met deze image.) In het geval van Flask apps wordt standaard poort 5000 gebruikt:
```Dockerfile
EXPOSE 5000
```
Tot slot bepalen we welk commando uitgevoerd moet worden wanneer de containers opstarten. (Je kan hier dus ook je eigen script voor gebruiken):
```Dockerfile
# [<commando>, <parameters>]
ENTRYPOINT ["bash", "../entrypoint.sh"]
```

{{% notice warning %}}
We maken nu enkele files aan in Windows die we in onze Linux container gaan gebruiken. Een belangrijk aandachtspunt hierbij is dat Windows standaard CRLF (Carriage Return + Line Feed) gebruikt als line endings, terwijl Linux en Unix systemen LF (Line Feed) gebruiken. CRLF voegt een extra karakter toe aan het einde van elke regel (`\r\n`), terwijl LF alleen een line feed karakter gebruikt (`\n`). Als je scripts (zoals `entrypoint.sh`) aanmaakt in Windows en deze in een Linux container wilt uitvoeren, moet je ervoor zorgen dat de line endings worden omgezet van CRLF naar LF. Anders kunnen er fouten optreden bij het uitvoeren van scripts in de container. Dit kan je in de meeste code editors zoals Notepad++ of VSCode eenvoudig doen.
{{% /notice %}}

**Meer info over de verschillende commando's en nog meer commando's kan je [hier](https://kapeli.com/cheat_sheets/Dockerfile.docset/Contents/Resources/Documents/index) vinden. Of via de [officiële docker documentatie](https://docs.docker.com/build/).**

Je kan nu je eigen image builden met het volgende commando:
```bash
$ docker build . -t my_image_name
```

Nu kan je containers starten op basis van jouw image `my_image_name`.

**Let op: De image wordt gecashed voor een performante opstart van containers, dit wil zeggen dat als je, nadat je een image 1 keer gebuild hebt, wijzigingen aanbrengt in de `Dockerfile` deze wijzigingen niet toegepast worden als je de vorige versie van je image niet hebt verwijderd**

**Extra weetje over Docker Build Optimalisatie: Layer Caching**<br/>
Docker optimaliseert het builden van images door gebruik te maken van **layer caching**. Elke instructie in een Dockerfile (zoals `FROM`, `COPY`, `RUN`, etc.) creëert een nieuwe layer in de image. Wanneer je een image opnieuw bouwt, controleert Docker of de inhoud van elke layer veranderd is. Als een layer ongewijzigd is gebleven, hergebruikt Docker de gecachte versie van die layer. 

Zodra Docker een verandering detecteert in een bepaalde layer, worden alle daaropvolgende layers opnieuw gebouwd - zelfs als hun inhoud niet veranderd is. Dit betekent dat de volgorde van instructies in je Dockerfile belangrijk is voor de build performance. Plaats bijvoorbeeld `COPY` instructies voor vaak veranderende bestanden (zoals source code) zo laat mogelijk in de Dockerfile, en installeer dependencies eerder. Op deze manier hoeven de dependency layers niet opnieuw gebouwd te worden bij elke code wijziging.

#### Wat voer ik uit in de dockerfile, wat in het entrypoint commando en wat binnen de container zelf.
- Alle commando's in de dockerfile worden 1 keer uitgevoerd bij het builden van de image. Hier kiezen we dus alles wat ons operating systeem instelt naar onze noden. Bijvoorbeeld configuratiefiles instellen, programma's installeren ... Dingen die dus niet snel veranderen. (Hier ga je ook vaak git repositories pullen van github.)
- Commando's in de entrypoint zullen bij het opstarten van de container steeds opnieuw uitgevoerd worden. Hier plaats je dus op het einde het commando dat als laatste moet blijven runnen op de container. Het is echter ook interessant om hier bijvoorbeeld dependencies te laten installeren omdat dit wel eens kan veranderen en zo hoef je niet steeds opnieuw een image te builden (zie dan wel dat je requirements.txt in een volume zit dat gekoppeld is aan een folder in je host). Bijvoorbeeld voor onze Flask app ziet de `entrypoint.sh` er als volgt uit:
```bash
#!/bin/bash

# install dependencies
pip3 install --no-cache-dir --break-system-packages -r /app/requirements.txt

# run command
tail -f /dev/null
```

**Het commando `tail -f /dev/null` is een speciaal commando dat je container gewoon oneindig laat draaien zonder iets te doen. Dit is handig voor DEVELOPMENT omgevingen omdat je daar zelf services wil starten. Voor onze LIVE Flask environment zal dit niet nodig zijn en starten we gewoon onze Flask service met `python3 app.py`.**

- Commando's die je binnen in de container zelf uitvoert kan vanalles zijn, maar let hierbij op dat alle systeem veranderingen die je maakt best ook in de image bijkomen. Andere specifieke veranderingen kan je namelijk opslaan door gebruik te maken van docker volumes. 

_Tip: Weet je niet welke programma's je image allemaal gaat nodig hebben of hoe je ze correct installeert? Voer dan de commando's gewoon eerst in de container uit tot alles werkt en neem dan die commando's over in je `Dockerfile`_


#### Wat/welke files sla ik op in de image vs docker volumes.
- Gelijkaardig aan de commando's moet je alle systeem files en dergelijk initialiseren in de image.
- Aanpassingen die dan per containers anders zullen zijn, kan je opslaan met docker volumes.


#### de docker-compose file
```yml
services:
  docker_build_files_demo1_pythondev:
    container_name: docker_build_files_demo1_pythondev
    restart: unless-stopped
    build:
      context: .
      dockerfile: ./Dockerfile
    ports:
      - 5001:5000
    volumes:
      - ./app:/app
```

_De rest van de files specifiek aan onze Flask applicatie kan je op de github terugvinden. (zie menu)_

### Demo 2: Python Flask live omgeving. 
#### Dockerfile
```Dockerfile
FROM python:3.10

WORKDIR /app

USER root
EXPOSE 5000

COPY ./app .
COPY ./entrypoint.sh ../entrypoint.sh

RUN chmod +x ../entrypoint.sh
ENTRYPOINT ["bash", "../entrypoint.sh"]

```
#### entrypoint.sh
```sh
#!/bin/bash
pip3 install --no-cache-dir --break-system-packages -r /app/requirements.txt
python3 /app/app.py
```
#### docker-compose.yml
```yml
services:
  docker_build_files_demo2_pythonlive:
    container_name: docker_build_files_demo2_pythonlive
    restart: unless-stopped
    build:
      context: .
      dockerfile: ./Dockerfile
    ports:
      - 5009:5000
```

### Exercise 1: 
Maak een `Dockerfile` en een `docker-compose.yml` file die een C development environment opzet zodat je met het volgende commando `make build && make run` in de container het kleine C programma in `./myfiles` kan compileren en runnen met behulp van GCC. Zorg ervoor dat de `./myfiles` met een volume gebonden is aan de container zodat je vanaf de host wijzigingen kan aanbrengen.

TIPS: Gebruik een geschikte base image en denk na welke programma's je nodig hebt om C applicaties te compilen en runnen.

{{% notice info %}}
**Tip**: In plaats van een `ENTRYPOINT` in je Dockerfile te definiëren, kan je ook het commando direct in je docker-compose.yml file meegeven met het `command` veld. Bijvoorbeeld:
```yml
services:
  my_service:
    # ... andere configuratie
    command: tail -f /dev/null
```
Dit overschrijft het `ENTRYPOINT` of `CMD` uit de Dockerfile en is handig voor development omgevingen.
{{% /notice %}}

### Exercise 2:
Maak een `Dockerfile` en een `docker-compose.yml` file die een Java omgeving opzet.
Zorg ervoor dat wanneer je deze container runt, bij het starten het java programma in `./myfiles` gecompileerd en uitgevoerd wordt. (gebruik hier een `entrypoint.sh` file voor)

Gebruik een java OpenJDK 21 image

TIPS: Gebruik een geschikte base image en denk na welke programma's je nodig hebt om Java applicaties te compilen en runnen.