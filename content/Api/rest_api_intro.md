---
title: "REST api theorie + CORS"
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

### Onderdelen van een HTTP Request

Een HTTP request bestaat uit vier belangrijke onderdelen:

1. **URL**: Dit is de locatie van de resource waartoe je toegang wilt krijgen. Bijvoorbeeld: `https://api.example.com/api/users`
2. **Method**: De HTTP methode geeft aan wat je met de resource wilt doen. Veelgebruikte methoden zijn `GET`, `POST`, `PUT`, `DELETE`. In dit voorbeeld gebruiken we de `POST` methode, die bedoeld is om data naar de server te verzenden.
3. **Headers**: In de headers kunnen aanvullende gegevens worden meegegeven, zoals informatie over het content type of authenticatie. Bijvoorbeeld: `Content-Type: application/json`.
4. **Body**: De body bevat de data die je naar de server wilt sturen, bijvoorbeeld in JSON-formaat.

#### Voorbeeld: in JavaScript

```js
fetch("http://localhost:5000/api/users", 
      {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: '{"name":"name", "email":"email"}'
      })
```

### Hoe werkt CORS (Cross-Origin Resource Sharing)?

CORS staat voor **Cross-Origin Resource Sharing** en is een beveiligingsmechanisme dat door browsers wordt toegepast. Het bepaalt of een webapplicatie die op een bepaalde "origin" (domein, protocol en poort) draait, toegang heeft tot resources van een andere "origin". Standaard mogen webpagina’s alleen verzoeken doen naar dezelfde origin als de pagina zelf, een beveiligingsmaatregel genaamd **same-origin policy**. CORS breidt deze restricties uit door cross-origin verzoeken onder bepaalde voorwaarden toe te staan.

Sinds onze API juist dienen om door andere websites gebruikt te worden proberen we deze policy steeds te enabelen.

### CORS-werking:

1. **Preflight Request**: Voor sommige HTTP-verzoeken stuurt de browser eerst een `OPTIONS` verzoek, een zogenaamd preflight request, naar de server om te controleren of het cross-origin verzoek is toegestaan. Dit gebeurt met methodes zoals `POST`, `PUT` of `DELETE`, of wanneer specifieke headers worden gebruikt. De server moet hierop reageren met toegestane methodes en headers.

2. **CORS Headers**: De server reageert met speciale **CORS headers** die aangeven of cross-origin verzoeken zijn toegestaan. Belangrijke headers zijn:
   - `Access-Control-Allow-Origin`: Deze header bepaalt welke origins toegang hebben. Dit kan een specifieke origin zijn (`https://example.com`) of een wildcard (`*`), wat betekent dat alle origins toegang hebben.
   - `Access-Control-Allow-Methods`: Hierin staat welke HTTP-methodes (bijvoorbeeld `GET`, `POST`, `PUT`, etc.) zijn toegestaan voor cross-origin verzoeken.
   - `Access-Control-Allow-Headers`: Deze header specificeert welke headers mogen worden meegestuurd met het cross-origin verzoek.
   - `Access-Control-Allow-Credentials`: Als deze op `true` is gezet, wordt het mogelijk om credentials zoals cookies of authorization tokens mee te sturen met cross-origin verzoeken.

3. **Voorbeeld flow**:  
   Een webpagina op `https://www.mijnanderewebsite.com` wil een API aanroepen op `https://www.mijnwebsite.com`. De browser stuurt een preflight `OPTIONS` request naar `https://www.mijnwebsite.com` met de vraag of deze origin (`https://www.mijnanderewebsite.com`) een verzoek mag doen. Als de server dit toestaat, stuurt hij een response met de benodigde CORS headers. Daarna stuurt de browser het daadwerkelijke verzoek.


