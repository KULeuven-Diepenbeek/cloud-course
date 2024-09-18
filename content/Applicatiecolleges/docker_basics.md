---
title: "Docker Basics"
weight: 1
author: Arne Duyver
draft: false
---

### Demo 1: Hello World

```bash
$ docker run hello-world
```
Merk op dat de container automatisch stopt.

### Demo 2: Static Website
Run de volgende container:
```bash
$ docker run dockersamples/static-site:latest
```
Merk op dat:
- de terminal geblokkeerd wordt. Waarom is dit? Omdat de container een commando uitvoert dat oneindig lang runt.
- we kunnen kijken welke containers allemaal actief zijn via volgend commando `$ docker ps`. (hiervoor moet je dus wel een nieuwe terminal gebruiken)
- we in het menu dat verschijnt heel wat info over de container kunnen zien zoals: de container ID, de image name, het commando dat gerunt wordt, wanneer de container aangemaakt werd, hoelang de container al actief is, welke poorten gebruikt worden door de container en ten slotte de naam van de container.
```bash
CONTAINER ID   IMAGE                              COMMAND   CREATED          STATUS    PORTS    NAMES
c298a80f2a79   dockersamples/static-site:latest   "/bin…"   Up 6 minutes     Up 6 ...           quirky_liskov
```
- we de gestopte container "hello-world" van demo 1 niet in de lijst zien staan. Om ook alle gestopte containers te bekijken gebruik je de `-a` flag: `$ docker ps -a`. De status geeft nu weer wanneer de container als laatste actief was.
```bash
CONTAINER ID   IMAGE                              COMMAND   CREATED          STATUS    PORTS    NAMES
c298a80f2a79   dockersamples/static-site:latest   "/bin…"   Up 6 minutes     Up 6 ...           quirky_liskov
2d416ef83c01   hello-world                        "/hello"  About a min...   Exited...          kind_aryabhata
...
```
- docker zelf namen verzint voor zijn containers.

Het is echter een beetje stom dat we een terminal moeten openhouden om een container in leven te kunnen laten. Daarom kunnen we een container ook starten in "detached" mode. (losgekoppeld van de terminal) Om dit te doen gebruik je de `-d` flag:
```bash
$ docker run -d dockersamples/static-site:latest
```
- we kunnen een lopende container stoppen door het volgende commando te gebruiken. `$ docker stop <container>` waarbij `<container>` (een deel) van de container id moet zijn OF de container naam.

_Voer dezelfde stappen als in de demo zelf uit, maar gebruik nu de docker container in detached mode._

Je zal nu ongetwijfeld opmerken dat we steeds containers bijmaken in plaats van ze te overschrijven (wat misschien je bedoeling is). Een container kunnen we nooit zomaar overschrijven omdat het dezelfde image heeft. We kunnen namelijk containers hebben die verschillend zijn van elkaar maar toch dezelfde image gebruiken. Daarom moeten we containers die we niet meer gebruiken specifiek verwijderen. Dit doe je met volgende commando (waarbij `<container>` weer (deel van) de container id kan zijn of de naam van de container):
```bash
$ docker rm <container>
```
Wil je een gestopte container toch terug starten, gebruik dan niet het `run` commando maar het `start` commando: `$ docker rm <container>`

Aangezien docker zelf soms rare/cryptische namen geeft aan de containers, is het een goede gewoonte om je containers steeds zelf een overzichtelijke naam te geven. Dit kunnen we doen zoals in onderstaand voorbeeld met de `--name` flag:
```bash
$ docker run --name static_website -d dockersamples/static-site:latest
```
Merk op dat elke container een unieke naam moet hebben (met uitzondering van namen als onderdeel van een service in docker-compose, maar hier komen we later nog op terug)

#### Binding ports
Je zal ongetwijfeld al gemerkt hebben dat het 'hello-world' voorbeeld geen poort gebruikt, maar het 'static website' voorbeeld wel. Voor zij die 'Full-Stack Web Development' gevolgd hebben weten ongetwijfeld al waarom. Dit is natuurlijk de poort waarop je de geserveerde website kan raadplegen.

Probeer eens naar het opgegeven adres te surfen met je webbrowser. Spoiler alarm ... je zal niets te zien krijgen. Dit komt omdat de poort opengesteld is in de container, maar niet in onze host. Hier komt dan ook het zeer mooie van docker tevoorschijn: We kunnen poorten van de container verbinden met poorten van de host EN nog mooier, dit hoeft niet dezelfde poort te zijn. Als je nog niet helemaal begrijpt waarom dit zo een geweldige feature is, geen probleem ik leg het hieronder uit nadat we zelf even de poorten verbinden. Dit doen we met de `-p` flag ( Voor zij die 'XAMP' nog hebben opstaan op hun pc zet die eerst af):
```bash
$ docker run --name static_website -p 80:80 -d dockersamples/static-site:latest
```

En zoals beloofd hier de uitleg over het probleem/de oplossing. Voor zij die 'XAMP' nog hebben opstaan op hun pc en de Apache server hebben opstaan, zouden nu misschien een error melding krijgen in de aard van `Ports are not available`. Dit komt omdat Apache poort 80 al gebruikt. Nu is het zo dat poort 80 een zeer populaire poort is voor het hosten van webpagina's. Om software zoals apache en NGINX te vertellen dat ze een andere poort moeten gebruiken heb je vaak veel moeite nodig, maar met docker kunnen we simpelweg een andere host port binden aan de poort 80 in de container. Hiermee hoeven we nooit te prutsen met vervelende configuratie files van Apache of Nginx. YES.

_Run de container opnieuw maar bind nu een andere random host port aan de container poort 80. BELANGRIJK het eerste poortnummer is steeds de host port en de poortnummer achter de ':' is steeds de port van de container_

Je kan nu je eigen website bewonderen op [http://localhost:de_poortnummer_die_je_juist_gekozen_hebt](http://localhost:80).

#### Interact with container
Ok, we kunnen nu een voorgeprepareerde website hosten, maar wat doen we nu als we de content van de webpagina willen aanpassen. Want voorlopig kennen we nog geen manier om direct met files te werken die in onze container staan. Dit gaan we dus nu doen:

Zoals we in de het `docker ps` menu hebben opgemerkt voeren de verschillende containers verschillende commando's uit bij het starten van de container. We kunnen dit commando aanpassen naar ons eigen commando. Op deze hackerige manier kunnen we een bash shell starten in onze container waarmee we kunnen interageren:
```bash
$ docker run --name static_website -p 9090:80 -it dockersamples/static-site:latest /bin/bash
```
De flag `-i` staat voor interactive, de flag `-t` staat voor [tty](https://stackoverflow.com/questions/22272401/what-does-it-mean-to-attach-a-tty-std-in-out-to-dockers-or-lxc) (een soort pseudo terminal) en `/bin/bash` is het commando dat we willen uitvoeren nl. een bash shell starten.
**LET OP: dit overschrijft dus het gewenste commando dat uitgevoerd moest worden. Een betere manier om te interageren met de container zien we hier iets verder.**

In dit voorbeeld zou het start command de Nginx server opstarten om onze webpagina te hosten, dit is dus niet meer het geval. We kunnen echter onze server nu manueel opstarten in de terminal met `# nginx -g 'daemon off;'`. (Waarom de website er nu anders uitziet, komen we zo dadelijk op terug, wil je onze oude vertrouwde website zien, voeg dan `/Hello_docker.html` toe aan de url)

Je kan de server stoppen met `Ctrl+c` en de container terminal verlaten met het `exit` command.

#### Environment variables
Laten we nu dat start commando van de originele static website image nog eens even bekijken (Je kan het volledige commando bekijken met  `$ docker ps --no-trunc`):
```bash
$ /bin/sh -c 'cd /usr/share/nginx/html && sed -e \"s/Docker/$AUTHOR/\" Hello_docker.html > index.html ; nginx -g 'daemon off;''
```
Laten we dit even opsplitsen:
- Met `/bin/sh -c` runnen we een shell command en de `-c` flag geeft aan dat we het commando in string vorm gaan meegeven.
- Het laatste deel `nginx -g 'daemon off;` kennen we ondertussen ook al. We starten gewoon de Nginx server.
- Je kan misschien al raden wat we in het midden deel doen: de content van onze webpagina instellen: `cd /usr/share/nginx/html && sed -e \"s/Docker/$AUTHOR/\" Hello_docker.html > index.html`. We gaan naar de juist directory met `cd ...` en met `Hello_docker.html > index.html` overschrijven we de inhoud van 'index.html' met de inhoud van 'Hello_docker.html'. Het enige vreemde wat we doen is met `sed -e \"s/Docker/$AUTHOR/\"`. Dit kleine scriptje vervangt de eerste keer dat het woord "Docker" voorkomt in het bestand 'Hello_docker.html' door de waarde van de omgevingsvariabele $AUTHOR wanneer het de andere file overschrijft. (in dit geval is $AUTHOR gelijk aan Docker, wat je ook kan zien als je `# echo $AUTHOR` zou ingeven als commando in de container terminal)

Wat is nu juist een omgevingsvariabele/environment variable? Dit zijn simpelweg sleutels/keys die opgeslagen zijn in je computersysteem die je dan een bepaalde waarde/value kan toekennen. Of ze krijgen automatisch een waarde dankzij het systeem.

In het voorbeeld is de waarde van `$AUTHOR` standaard ingesteld als 'Docker'. We kunnen environment variables van containers echter aanmaken en aanpassen bij het opstarten van de container met de `-e` flag. Zo kunnen we onze eigen naam gebruik als author in het voorbeeld en komt onze eigen naam tevoorschijn op de website:
```bash
$ docker run --name static_website -p 9090:80 -e AUTHOR=YourName -d dockersamples/static-site:latest
```
**Je kan bij flags vaak ook meerdere opties meegeven, zo kan je ook meerdere environment variables meegeven op volgende manier: `$ docker run --name static_website -p 9090:80 -e AUTHOR=YourName -e SITE_NAME=MyWebsite -d dockersamples/static-site:latest`. Gelijkaardig kan je ook meerdere poorten binden**

#### Attach terminal to a running container
Zoals beloofd gaan we nu ook een manier zien om aan een lopende container een terminal toe te voegen. Zo kom je niet in de weg van het startcommando van de container:
```bash
$ docker exec -it static_website /bin/bash
```
We specificeren hier weer dat we met een 'interactive' 'tty' terminal het commando 'bin/bash' willen 'executen' (`exec`) in de container 'static_website'.

_Maak met het volgende command een nieuwe html file aan `$ touch test.html` en steek als tekst in deze file 'hello there' via volgende commando `$ echo "hello there" > test.html`. Bekijk of het gelukt is door `/test.html` achter de url toe te voegen_

##### Attach VSCode
We kunnen echter ook een gehele VSCode omgeving opstarten in onze container en zo alle files aanpassen. (Let op: die VSCode instantie is eigenlijk een VSCode server die draait op je container dus die neemt je extensies en settings dus niet over.)

Je doet dit door op het docker-icoon te klikken in VSCode. Dan zie je alle containers en images op je systeem. Je ziet ook wel containers runnen en welke niet. Je kan via deze weg containers dus ook starten en stoppen (en nog veel meer). Nu zijn we echter vooral geïnteresseerd in de optie 'Attach VSCode'. Doe hiervoor een rechtermuisklik op je static_website container en klik op 'Attach VSCode'. Merk op dat je ook een simpele shell kan attachen. Je kan nu in het nieuwe VSCode scherm dat verschijnt mappen van in de container openen, files aanpassen, terminals in de container openen ...

_Maak nu nog meer aanpassingen in de test.html file en kijk of ze getoond worden op de website._

#### Docker persistance and docker volumes
Stel dat je nu enorm veel moeite hebt gestoken in die nieuwe website en je wil de container even verwijderen (omdat er bijvoorbeeld een nieuwere image uit is gekomen) en opnieuw opstarten, wel dan dikke pech want al je aanpassingen aan de container kunnen wel eens verdwenen zijn.

Het principe van docker werkt namelijk zo: de hele development omgeving zou ingesteld moeten worden via de docker image. Alle aanpassingen worden dan niet standaard bijgehouden, je moet namelijk zelf specificeren welke files van je omgeving je wil behouden zo kan je snel wisselen tussen images zonder dat je alle onnodige files van vorige image blijft opslaan. We doen dit door gebruik te maken van docker volumes. We specificeren een directory in de container die we willen behouden en geven deze een naam.

- Eerst maken we het docker volume aan: `$ docker volume create static_website_volume`
- Dan koppelen we dit volume aan een directory in een container met de `-v` flag:
```bash
$ docker run --name static_website -p 9090:80 -e AUTHOR=YourName -v static_website_volume:/usr/share/nginx/html -d dockersamples/static-site:latest
```
_Indien je een docker volume verbind aan een container terwijl dit volume nog niet bestaat dan wordt dit automatisch aangemaakt_

**Je volume leeft dus apart van je container, maar is gekoppeld. Zo blijft de data bestaan ook al verwijder je de container. Je moet dan gewoon het volume koppelen aan de nieuwe container.**

Je moet docker volumes dan ook apart verwijderen als je dat wil:
- list alle volumes: `$ docker volume ls`
- remove een volume: `$ docker volume rm <volume_name>`

Je kan in de plaats van docker een docker volume te koppelen ook een directory van je host koppelen aan de container. Hiervoor gebruik je dan het absolute pad van de host directory (in sommige gevallen kan je ook een relatief pad gebruiken maar een absoluut pad is soms veiliger). De host directory komt voor de ':' en de container directory erachter:
```bash
$ docker run --name static_website -p 9090:80 -e AUTHOR=YourName -v ${PWD}/static_website_host:/usr/share/nginx/html -d dockersamples/static-site:latest
```

Deze methode heeft als voordeel dat je bijvoorbeeld je host code editor kan gebruiken (met al zijn extensies en features) om files aan te passen maar ze kunnen wel rechtstreeks gebruikt worden door de container.

_Speel eens even met deze methoden en kijk wanneer data verloren gaat, hoe de synchronisatie werkt tussen host en container ..._

### Exercise 1
- Gebruik de nginx base image en maak gebruik van docker volumes om je eigen statische website te tonen
<!-- TODO: DO this myself En put solutions on github -->

### Exercise 2
- Gebruik een db image en maak een database aan (je mag kiezen welke database je wil)
- Interageer met de database door middel van een attached console en de commandline interface van de database
<!-- TODO: DO this myself En put solutions here in comment so you can uncomment in lesson -->

### Exercise 3 (OUD): (werkt niet meer dus moet je niet doen. We komen hier later op terug)
- Breid de functionaliteit van je website uit [Exercise 1](#exercise-1) uit zodat deze via javascript calls maakt naar de database om data uit de database te displayen. (Je mag het ip-adres, de port, username, password en databasenaam hardcoded opslaan in de javascript)
<!--TODO: CHANGE EXERCSiSE DO this myself En put solutions here in comment so you can uncomment in lesson -->

### Exercise 3
- Maak een nieuwe container met een website zodat deze via javascript calls een HTTP GET request stuurt naar de website uit [Exercise 1](#exercise-1) wanneer je op een knop klikt en display het resultaat van de get request. (Je kan `localhost:port_van_de_container_uit1` gebruiken om je get request uit te voeren.)
<!--TODO: CHANGE EXERCSISE DO this myself En put solutions here in comment so you can uncomment in lesson -->

### Demo 3: Docker compose
In plaats dat we steeds een lang run commando moeten runnen zou het handig zijn al deze instellingen in een soort script te verzamelen. Dit kan en zo een file heet een `docker-compose.yml` file. We gebruiken yaml syntax om de juiste instellingen op te stellen. Merk hieronder ook op dat we gebruik maken van **services** hier komen we later nog op terug omdat we in deze compose file ook meerdere containers in een keer kunnen opstarten en nog veel meer.
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
- (meer restart policies vind je [hier](https://www.baeldung.com/ops/docker-compose-restart-policies))

