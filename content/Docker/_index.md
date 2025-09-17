---
title: "Docker introductie"
weight: 1
author: Arne Duyver
draft: false
---

## Inleiding tot Microservices en Docker
<!-- TODO voeg afbeeldingen toe bij de uitleg -->

### Wat zijn Microservices?

Microservices zijn een architecturale stijl waarbij een applicatie wordt opgebouwd uit **kleine, onafhankelijke services** die elk een specifieke taak uitvoeren.
In plaats van één groot monolithisch systeem te bouwen, wordt de software opgesplitst in kleinere delen die via API’s met elkaar communiceren.

#### Kenmerken van microservices

* **Kleine, zelfstandige modules** die apart ontwikkeld en gedeployed kunnen worden.
* Communicatie via **lichte protocollen** zoals HTTP/REST of gRPC.
* **Onafhankelijke schaalbaarheid**: enkel de services die meer capaciteit nodig hebben worden opgeschaald.
* **Foutenisolatie**: een fout in één service hoeft niet de hele applicatie plat te leggen.

---

### Wat is SaaS?

**SaaS (Software as a Service)** is een manier om software aan te bieden via het internet. In plaats van software lokaal te installeren, krijgen gebruikers toegang via een webbrowser of app.
De software draait in de cloud en de provider beheert de infrastructuur, updates en beveiliging.

#### Voorbeelden

* **Google Workspace (Docs, Gmail, Drive)**
* **Microsoft 365**
* **Slack**
* **Salesforce**

#### Voordelen van SaaS

* Geen installatie of onderhoud nodig bij de gebruiker.
* Toegang overal via internet.
* Automatische updates en beveiliging.
* Abonnementsmodel (betalen per gebruik of per maand).

---

### Virtual Machines (VM’s)

Voor Docker bestond, gebruikten ontwikkelaars vaak **Virtual Machines (VM’s)** om software te isoleren.

#### Hoe werkt een VM?

* Een VM draait op een **hypervisor** (bijv. VMware, VirtualBox, KVM).
* Elke VM bevat:
  * een volledig **besturingssysteem** (Windows/Linux),
  * de applicatie en alle afhankelijkheden.
* VM’s zijn volledig van elkaar geïsoleerd.

#### Nadelen van VM’s

* **Zwaar**: elke VM heeft een volledig OS, wat veel geheugen en opslag vraagt.
* **Trage opstart**: het kan minuten duren om een VM op te starten.
* **Inefficiënt**: veel duplicatie van OS-onderdelen.

---

### Wat is Docker?

**Docker** is een platform dat applicaties draait in **containers**.
Een container is een lichtgewicht, geïsoleerde omgeving die de applicatie en alle afhankelijkheden bevat, maar **het besturingssysteem deelt met de host**.

#### Voordelen van Docker t.o.v. VM’s

* **Sneller**: containers starten in seconden, geen volledig OS nodig.
* **Lichter**: containers delen de kernel met de host, minder duplicatie.
* **Flexibel**: eenvoudig te verplaatsen tussen laptops, servers en cloud.

---

### Hoe werkt Docker onder de motorkap?

1. **Docker Engine**
   De kern van Docker, verantwoordelijk voor het maken, draaien en beheren van containers.
2. **Namespaces**
   Zorgen voor **isolatie**:
   * Elke container heeft zijn eigen bestandssysteem, netwerkstack, process-ID’s.
   * Containers zien elkaars processen niet.
3. **Control Groups (cgroups)**
   Beperken hoeveel CPU, geheugen of I/O een container mag gebruiken.
   → Zo kan één container niet alle resources opsouperen.
4. **Union File System (UnionFS)**
   Docker images zijn opgebouwd in lagen.
   * Bijvoorbeeld: een laag met Ubuntu, een laag met Python, een laag met jouw code.
   * Hergebruik van lagen maakt images **compact en efficiënt**.
5. **Docker Images & Containers**
   * Een **image** is een blauwdruk (read-only).
   * Een **container** is een draaiende instantie van een image (met een writeable layer erbovenop).

---

### Alternatieven voor Docker

Docker is populair, maar er zijn ook andere containertechnologieën:

* **Podman**
  * Compatibel met Docker CLI.
  * Draait containers zonder een daemon, vaak veiliger in productie.
* **CRI-O**
  * Lichtgewicht container runtime, speciaal gemaakt voor Kubernetes.
* **containerd**
  * Een core container runtime (oorspronkelijk uit Docker gehaald).
  * Wordt vaak gebruikt als backend in Kubernetes.
* **LXC/LXD**
  * Lagere-niveau Linux containers.
  * Meer gericht op systeemcontainers (volledig Linux-systeem in een container).

---

### Docker Workflow

1. Schrijf een `Dockerfile` met instructies hoe een image gebouwd moet worden.
2. Bouw de image:
   ```bash
   docker build -t mijn-app .
   ```
3. Start een container:
   ```bash
   docker run -p 8080:8080 mijn-app
   ```
4. Deel de image via **Docker Hub** of een private registry.

---

### Samenvatting

* **Microservices**: kleine, zelfstandige services die samen een applicatie vormen.
* **SaaS**: software als online dienst via de cloud.
* **VM’s**: zwaar maar volledig geïsoleerd met een hypervisor.
* **Docker**: containers die lichtgewicht en snel zijn door het delen van de host kernel.
* **Alternatieven voor Docker**: Podman, CRI-O, containerd, LXC/LXD.
* **Onder de motorkap** gebruikt Docker namespaces, cgroups en UnionFS om containers te isoleren en efficiënt te maken.


## Applicatiecolleges

We gaan uiteindelijk in de applicatiecolleges verschillende api's aanmaken. Belangrijk hierbij is dat die microservices gemakkelijk deployable zijn in de cloud. Om dit zo vlekkeloos mogelijk te laten verlopen gaan we gebruik maken van docker containers. Docker containers zijn ook zeer handig om developer omgevingen op te zetten zoals virtual environments in python, maar dan nog meer flexibel.

### Docker installeren

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