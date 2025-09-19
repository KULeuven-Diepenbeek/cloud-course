---
title: "Api's consumeren"
weight: 2
author: Arne Duyver
draft: true
---
## Wat is een API?

Een API (Application Programming Interface) is een gestructureerde manier waarop verschillende softwarecomponenten met elkaar communiceren. In de context van webontwikkeling verwijst "API" vaak naar een netwerkgebaseerde interface die het mogelijk maakt dat clients (zoals web- of mobiele applicaties) gegevens opvragen, wijzigen of laten verwerken door een server. Een API definieert welke verzoeken toegestaan zijn, welke gegevensformaten worden gebruikt en welke reacties verwacht mogen worden. Dit concept maakt het loskoppelen van componenten mogelijk: de client hoeft niet te weten hoe de server intern werkt, alleen welke contracten (endpoints, velden, types) beschikbaar zijn.

APIs spelen een centrale rol in moderne software-architecturen: microservices communiceren via API's, mobiele apps gebruiken API's om data van een centrale backend te laden en third-party services bieden API's aan zodat externe ontwikkelaars functionaliteit kunnen gebruiken. Naast technische specificaties omvat het begrip API ook ontwerpkeuzes, versiebeheer, authenticatie en documentatie—allemaal essentiële onderdelen voor een betrouwbare en veilige interface.

### Doel van deze pagina

Deze pagina biedt een beknopt maar volledig overzicht van vier veelgebruikte API-architecturen: REST, SOAP, GraphQL en gRPC. Per type leggen we uit wat het is, hoe het globaal werkt en behandelen we de belangrijkste voordelen en nadelen. Afsluitend staat er een compacte vergelijkingstabel die de belangrijkste eigenschappen naast elkaar zet.

## REST

REST (Representational State Transfer) is geen protocol maar een architectuurstijl die principes en beperkingen beschrijft voor het ontwerpen van gedistribueerde systemen. RESTful API's gebruiken doorgaans HTTP als transportlaag en maken veelal gebruik van de standaard HTTP-methoden (GET, POST, PUT, PATCH, DELETE) om bewerkingen op resources uit te voeren. Resources worden meestal als URL's gemodelleerd en data wordt vaak uitgewisseld in JSON-formaat.

Hoe REST werkt in de praktijk: een client stuurt een HTTP-verzoek naar een specifieke resource-URL; de server verwerkt dat verzoek en retourneert een representatie van de resource (bijvoorbeeld JSON), samen met een passende HTTP-statuscode. REST stimuleert stateless communicatie, wat betekent dat elk verzoek alle benodigde informatie moet bevatten zodat de server het verzoek onafhankelijk kan afhandelen.

Voordelen van REST:

- Eenvoudig en intuïtief ontwerp dat goed aansluit op HTTP-principes.
- Brede adoptie en uitstekende ondersteuning in frameworks, libraries en tooling.
- Goed geschikt voor schaalbare, cachebare resources en eenvoudige CRUD-workflows.

Nadelen van REST:

- Kan leiden tot over-fetching of under-fetching bij complexe of sterk geneste data.
- Geen verplicht, ingebouwd type- of contractsysteem (behalve via additionele specificaties zoals OpenAPI).
- Bij complexe relaties zijn vaak meerdere endpoints nodig of zijn extra ontwerpafspraken vereist.

## SOAP

SOAP (Simple Object Access Protocol) is een protocol voor het uitwisselen van gestructureerde informatie in gedistribueerde omgevingen. SOAP-berichten gebruiken XML als berichtenformaat en definiëren een streng XML-gestructureerd enveloppe-schema. SOAP komt vaak samen met WSDL (Web Services Description Language), waarmee een zeer gedetailleerde servicecontractbeschrijving wordt geleverd, inclusief beschikbare operaties, berichten en datatypes.

In de praktijk stuurt een client een SOAP-bericht (een XML-enveloppe) naar een endpoint, waarop de server een beantwoordt met een ander SOAP-bericht. SOAP specificeert ook uitbreidingen voor beveiliging, transacties en berichtrouting, waardoor het geschikt is voor enterprise-omgevingen met strikte eisen aan betrouwbaarheid en interoperabiliteit.

Voordelen van SOAP:

- Zeer formele specificatie met sterke contractbeschrijving via WSDL.
- Uitgebreide standaarden voor beveiliging, betrouwbaarheid en transacties (bijv. WS-Security, WS-ReliableMessaging).
- Goede interoperabiliteit tussen enterprise-systemen en tooling voor veel programmeertalen.

Nadelen van SOAP:

- Complex en verhalend (veel XML-overhead), wat parsing- en bandbreedtekosten verhoogt.
- Minder populair voor moderne web- en mobiele APIs; steilere leercurve voor nieuwkomers.
- Often overkill voor eenvoudige CRUD- of microservice-scenario's.

## GraphQL

GraphQL is een querytaal en runtime voor APIs die is ontworpen om precies te leveren wat clients nodig hebben. In plaats van meerdere endpoints voor verschillende resources biedt een GraphQL-server een enkele endpoint waarop clients flexibele queries kunnen uitvoeren. De client specificeert in de query exact welke velden en relaties hij wil terugkrijgen, waardoor het probleem van over- en under-fetching sterk verminderd wordt.

Een GraphQL-interactie bestaat meestal uit het sturen van een query (of mutatie) naar de server, eventueel met variabelen, en het ontvangen van een gestructureerd JSON-resultaat dat precies de aangevraagde vorm heeft. GraphQL vereist een schema dat types en relaties definieert; dit schema vormt het contract tussen client en server en is zelfbeschrijvend, wat helpt bij tooling en documentatie.

Voordelen van GraphQL:

- Clients kunnen exact de benodigde velden opvragen, waardoor over- en under-fetching afneemt.
- Sterk, zelfbeschrijvend schema dat tooling en typecontrole verbetert.
- Zeer geschikt voor complexe, gelinkte data en samenvoegen van meerdere bronnen achter één endpoint.

Nadelen van GraphQL:

- Meer complexe serverlogica (resolvers, zorg voor efficiënte datatoegang en voorkomen van N+1-problemen).
- Caching op HTTP-niveau is moeilijker; fijne caching vereist vaak extra infrastructuur.
- Autorisatie en rate-limiting kunnen complexer worden omdat queries dynamisch zijn.

## gRPC

gRPC is een moderne, high-performance open-source RPC (Remote Procedure Call) framework ontwikkeld door Google. gRPC gebruikt HTTP/2 als transportlaag en maakt meestal gebruik van Protocol Buffers (protobuf) als compact, binair serialisatieformaat voor berichten en servicedefinities. Met gRPC definieer je services en methodes in een .proto-bestand, waarop automatisch type-safe client- en serverstubcode kan worden gegenereerd.

In gebruik roept een client remote procedures op alsof het lokale methodes zijn. gRPC ondersteunt verschillende communicatiepatronen: unary requests, server-side streaming, client-side streaming en bidirectionele streaming. Door het gebruik van HTTP/2 en een binair formaat is gRPC zeer efficiënt voor lage-latentie communicatie en voor situaties met hoge doorvoer.

Voordelen van gRPC:

- Hoge prestaties en efficiënt netwerkgebruik dankzij HTTP/2 en binair serialisatieformaat (Protocol Buffers).
- Sterke type-safety en automatische codegeneratie voor clients en servers.
- Ingebouwde ondersteuning voor verschillende communicatiepatronen waaronder streaming en bidirectionele koppelingen.

Nadelen van gRPC:

- Binair formaat is minder makkelijk leesbaar en te debuggen dan JSON/XML.
- Directe browserondersteuning is minder eenvoudig; vaak is gRPC-Web of proxying nodig.
- Vereist meer setup (proto-bestanden, codegeneratie) en heeft een hogere leercurve voor teams die geen ervaring hebben met RPC-workflows.

## Vergelijking

Hieronder staat een compacte tabel die de vier volwassen API-typen langs een paar belangrijke eigenschappen legt zodat je snel de verschillen kunt zien.

| Type     | Dataformaat | Transport | Sterke punten | Zwakke punten |
|----------|-------------|-----------|----------------|----------------|
| REST     | Meestal JSON (soms XML) | HTTP/1.1 of HTTP/2 | Eenvoudig, wijdverspreid, goed ondersteund door tooling | Over/under-fetching bij complexe queries; geen strikt schema ingebouwd |
| SOAP     | XML         | HTTP, SMTP of andere | Formeel contract (WSDL), uitgebreide beveiligings- en transactie-extensies | Complex, verbose (XML), minder populair voor nieuwe projecten |
| GraphQL  | JSON (via query-resultaat) | HTTP (vaak POST) | Flexibele queries, voorkomt over/under-fetching, sterk schema | Servercomplexiteit, caching en autorisatie lastiger |
| gRPC     | Binair (Protocol Buffers) | HTTP/2    | Hoge prestatie, streaming, type-safe codegeneratie | Minder browservriendelijk, binair formaat minder inspecteerbaar |

Hoewel elk van deze benaderingen overlap heeft in waar ze voor gebruikt kunnen worden, bepalen schaalbehoefte, interoperabiliteitseisen, ontwikkel- en onderhoudsvoorkeuren en bestaande infrastructuur meestal welke oplossing het beste past. Voor openbare web-API's hebben organisaties vaak REST of GraphQL, terwijl enterprise-omgevingen soms SOAP gebruiken en interne microservice-communicatie vaak baat heeft bij gRPC.

Als vervolgstap kun je per gekozen type concrete voorbeelden of oefenopdrachten toevoegen: een simpele REST-API met CRUD-operaties, een minimale SOAP-service met WSDL, een GraphQL-schema met resolvers en een gRPC-service met streaming. Deze praktische uitwerkingen helpen studenten de concepten te verduidelijken en toepassen.
