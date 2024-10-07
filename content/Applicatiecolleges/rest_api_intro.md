---
title: "REST api intro"
weight: 4
author: Arne Duyver
draft: false
---

Een **REST API** (REpresentational State Transfer Application Programming Interface) is een manier waarop systemen met elkaar kunnen communiceren via het internet, meestal via het HTTP-protocol. Het maakt gebruik van standaardmethodes zoals **GET**, **POST**, **PUT**, **DELETE** om data op te vragen, toe te voegen, te wijzigen of te verwijderen. 

### REST en CRUD
REST APIs volgen vaak het **CRUD**-principe, wat staat voor **Create**, **Read**, **Update**, en **Delete**. Deze vier basisbewerkingen komen overeen met de HTTP-methodes die in een REST API worden gebruikt:

- **Create (POST)**: Nieuwe gegevens toevoegen.
- **Read (GET)**: Gegevens opvragen of lezen.
- **Update (PUT/PATCH)**: Bestaande gegevens bijwerken.
- **Delete (DELETE)**: Gegevens verwijderen.

CRUD bewerkingen vormen de kern van veel API’s omdat ze de basisfunctionaliteiten bieden voor data manipulatie. In REST worden deze bewerkingen gekoppeld aan de HTTP-methodes:

| CRUD Actie | REST Methode | Voorbeeld        |
|------------|--------------|------------------|
| Create     | POST         | `POST /books`    |
| Read       | GET          | `GET /books/1`   |
| Update     | PUT/PATCH    | `PUT /books/1`   |
| Delete     | DELETE       | `DELETE /books/1`|

### REST als Architectuur
REST is geen protocol, maar een **architectuurstijl**. Dit betekent dat het geen vaste regels of standaarden oplegt, maar een set van principes en beperkingen biedt om een API op te bouwen. REST richt zich op de volgende principes:
- **Stateless**: Elke API-aanroep staat op zichzelf, zonder afhankelijkheid van vorige verzoeken.
- **Uniforme Interface**: Alle interacties met resources gebeuren op een consistente manier, vaak via uniforme HTTP-methodes zoals GET, POST, PUT en DELETE.
- **Resource-gebaseerd**: In plaats van acties te beschrijven, richt REST zich op resources (zoals gebruikers of boeken) die via een URL worden aangesproken.

_REST volgt dus geen vaste standaarden. Je kan dus evengoed in een HTTP DEL een nieuw item gaan aanmaken, maar dat is natuurlijk slecht implementeren van de architectuur._

Omdat REST een architectuurstijl is, biedt het ontwikkelaars veel vrijheid bij het implementeren van een API. Dit in tegenstelling tot **SOAP** (Simple Object Access Protocol), dat wel een **protocol** is en werkt met strikte standaarden en regels, inclusief XML-gebaseerde berichten en foutafhandeling.

In vergelijking met SOAP is REST lichter, flexibeler, en eenvoudiger te gebruiken, vooral in webapplicaties. SOAP biedt wel meer geavanceerde beveiligings- en foutafhandelingsmogelijkheden, maar de eenvoud van REST maakt het populairder voor moderne webdiensten. Het nadeel is natuurlijk dat je nooit zeker bent of de REST stijl correct gevolgd werd en hoe de data dus juist verwerkt wordt.

### Voorbeeld:
Stel dat je een API hebt voor het beheren van boeken in een bibliotheek:

- **GET /books**: Haal een lijst op van alle boeken.
- **GET /books/5**: Haal informatie op over het boek met ID 5.
- **POST /books**: Voeg een nieuw boek toe door de boekinformatie in het verzoek mee te sturen.
  - Hoe het nieuwe boek er moet uitzien dat je aanmaakt steek je in een JSON object.
- **PUT /books/5**: Update de gegevens van het boek met ID 5.
  - Wat de nieuwe waarden van boek 5 moeten zijn steek je in een JSON object.
- **DELETE /books/5**: Verwijder het boek met ID 5.

REST APIs zijn populair omdat ze simpel en schaalbaar zijn, en ze werken goed met webapplicaties.
