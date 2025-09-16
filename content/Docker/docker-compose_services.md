---
title: "Docker-compose services"
weight: 3
author: Arne Duyver
draft: true
---

Met [exercise 3]({{< ref "/Applicatiecolleges/docker_basics#exercise-3" >}} "Exercise 3") zal je ongetwijfeld al gemerkt hebben dat het moeilijk is om twee containers met elkaar te laten praten. Dit is natuurlijk de bedoeling want we willen verschillende containers namelijk afschermen van elkaar. 

Om toch te verbinden met de host kan je volgende adressen gebruiken:
- op Windows: `host.docker.local`
- op Linux (hoogstwaarschijnlijk): `172.17.0.1`

### Demo 1: Connect server to database

### Networking in docker
Het default netwerk dat docker gebruikt is van het type "bridge" en vormt letterlijk een brug tussen de host en de container. Elke container waarvoor je zelf geen specifiek netwerk instelt zal de "default bridge" gebruiken. Je kan zelf netwerken aanmaken en containers aan verbinden. Er bestaan verschillende types, maar degene die wij het meeste gaan gebruiken is de bridge. Andere interessante types zijn host (laat de container runnen alsof het letterlijk een deel vormt van het host netwerk. Container gebruikt dan dezelfde poorten als de host) en macvlan. Dit laatste type is redelijk complex maar zorgt ervoor dat je container op je (wifi)netwerk als een apart device beschouwd wordt met een eigen ip-adres. We gaan hier niet verder op in maar volgende video geeft een mooi overzicht van de verschillende mogelijkheden: [Docker networking - You need to learn it](https://www.youtube.com/watch?v=bKFMS5C4CG0)
Of je kan natuurlijk [de officiële documentatie](https://docs.docker.com/engine/network/) raadplegen.

### Networking in docker-compose
Wanneer je meerdere services plaatst in dezelfde docker-compose file wordt automatisch een nieuw bridge netwerk gemaakt en verbonden aan de host en al die services. Je kan dat contact opnemen met andere services door hun naam te gebruiken. Je hoeft dus niet hun ip address uit te pluizen. Hieronder zo een voorbeeld van de docker-compose file om een Flask applicatie data uit een database te laten halen (uit een andere container):

#### docker-compose file
```yml
version: '3.8'

services:
  server:
    container_name: flask_server
    restart: unless-stopped
    build:
      context: .
      dockerfile: ./Dockerfile
    ports:
      - 5008:5000
    volumes:
      - ./app:/app
    networks:
      - mynetwork
    # Met depends_on laat je weten dat eerst de "db" service moet starten omdat een deel/delen van deze container moeten communiceren met die service
    depends_on:
      - db

  db:
    image: mariadb
    restart: always
    environment:
      MARIADB_ROOT_PASSWORD: example
    volumes:
      - ./mydata:/var/lib/mysql:Z
    networks:
      - mynetwork

  adminer:
    image: adminer
    restart: always
    ports:
      - 9092:8080
    networks:
      - mynetwork

networks:
  mynetwork:
    driver: bridge
```
#### Flask app.py
```python
from flask import Flask, render_template_string
import pymysql

app = Flask(__name__)

# Database connection
def get_db_connection():
    return pymysql.connect(
        # GEBRUIK DE SERVICE NAAM "db" ALS IP-ADRES !!!
        host="db",
        user="root",
        password="example",
        database="school",
        charset='utf8mb4',
        cursorclass=pymysql.cursors.DictCursor
    )

    ...
```

_De volledige implementatie is terug te vinden op github. (zie menu)_



### Exercise 1: 
Maak analoog aan de demo twee containers aan maar gebruik nu een java container als frontend om de data uit de database te tonen. Je mag de data door je java programma gewoon in de console laten printen. TIP: maak gebruik van een build tool zoals Gradle. (Zoek dus even uit hoe je java image/container eruit moet zien.)
