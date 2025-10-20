---
title: "REST api documenteren"
weight: 8
author: Arne Duyver
draft: false
---

## Documenteren van een Flask REST API

Een goed gedocumenteerde **REST API** maakt het voor andere developers eenvoudiger om te begrijpen hoe de API werkt, welke endpoints beschikbaar zijn, welke parameters verwacht worden en wat de mogelijke responses zijn. Documentatie is dus essentieel, niet alleen voor extern gebruik, maar ook voor onderhoud en samenwerking binnen teams.

### Waarom API-documentatie belangrijk is

* **Communicatie**: Andere developers (of jijzelf in de toekomst) weten hoe ze met de API moeten werken.
* **Foutvermindering**: Duidelijke specificaties verkleinen de kans op verkeerde implementaties.
* **Automatisering**: Tools zoals Swagger of Postman kunnen automatisch documentatie en testinterfaces genereren.
* **Beheer**: Bij grote projecten helpt documentatie om consistentie te bewaren tussen verschillende API-versies.

### Manieren om een Flask API te documenteren

Er zijn drie veelgebruikte manieren om Flask APIs te documenteren:

1. **Inline docstrings** (manueel, in de code zelf)
2. **Automatische documentatie met Swagger (OpenAPI)** (Dit maakt gebruik van de Inline Docstrings)
3. **Markdown documentatie** (voor handleidingen of interne wiki’s)

Hieronder bespreken we elk van deze methodes.

#### 1. Inline docstrings in Flask routes

De eenvoudigste (en beste) manier om documentatie toe te voegen is door **docstrings** te gebruiken bij elke route. Dit is handig voor kleine projecten en die docstring kan dan later gebruikt worden voor het automatisch genereren van online documentatie.

```python
@app.route('/api/users', methods=['GET'])
def get_users():
    """
    Haalt alle gebruikers op uit de database.

    **Method:** GET  
    **Endpoint:** `/api/users`  
    **Query parameters:**
      - `pwd` (str): eenvoudig wachtwoord ter beveiliging
    
    **Responses:**
      - `200`: Lijst van gebruikers in JSON-formaat
      - `500`: Databasefout of andere serverfout
    """
    # Code hier
```

Deze aanpak houdt de documentatie dicht bij de code, maar is minder geschikt voor grotere projecten met veel routes.

#### 2. Automatische documentatie met Swagger (Flasgger)

Voor grotere APIs is het handig om **automatisch gegenereerde documentatie** te gebruiken via **Swagger (OpenAPI)**. Een populaire uitbreiding hiervoor is **Flasgger**.

##### Installatie

```bash
pip install flasgger
```

##### Voorbeeldimplementatie

```python
from flask import Flask, jsonify, request
from flasgger import Swagger

app = Flask(__name__)
Swagger(app)  # maakt automatische Swagger-documentatie aan op basis van docstring

@app.route('/api/users', methods=['POST'])
def create_user():
    """
    Maak een nieuwe gebruiker aan.
    ---
    tags:
      - Users
    parameters:
      - name: body
        in: body
        required: true
        schema:
          id: User
          required:
            - name
            - email
          properties:
            name:
              type: string
              description: Naam van de gebruiker
            email:
              type: string
              description: E-mailadres van de gebruiker
    responses:
      201:
        description: Gebruiker succesvol aangemaakt
      400:
        description: Foutieve of ontbrekende parameters
    """
    data = request.get_json()
    name = data.get('name')
    email = data.get('email')
    if not name or not email:
        return jsonify({'error': 'Name and email required'}), 400
    return jsonify({'message': 'User created successfully'}), 201

if __name__ == '__main__':
    app.run(debug=True)
```

##### Resultaat

Zodra je de server start, kun je naar [http://localhost:5000/apidocs](http://localhost:5000/apidocs) gaan. Je krijgt daar een interactieve Swagger-interface waarin je alle endpoints kunt testen en hun parameters kunt bekijken.

##### Wat hoort er in een docstring voor automatische Swagger-documentatie

Een **docstring** die Swagger (via bijvoorbeeld Flasgger) automatisch kan verwerken, moet gestructureerd zijn volgens de **OpenAPI-conventies**. Ze begint meestal met een korte beschrijving van de functionaliteit van het endpoint, gevolgd door een YAML-achtige structuur die Swagger gebruikt om documentatie te genereren. De aanbevolen volgorde en inhoud zijn:

1. **Korte beschrijving**: Een zin die uitlegt wat de route doet (één of twee regels).
2. **--- scheidingslijn**: Geeft aan dat wat volgt in YAML-formaat is voor Swagger.
3. **tags**: Een lijst met categorieën waaronder het endpoint valt (bijv. `Users`, `Auth`).
4. **parameters**: Lijst van verwachte invoerparameters, met vermelding van:

   * `name`: De parameternaam
   * `in`: Waar de parameter zich bevindt (`query`, `path`, `body`, `header`)
   * `required`: `true` of `false`
   * `type` en `description`: Datatype en uitleg
   * eventueel een `schema` als het een JSON-body betreft
5. **responses**: Beschrijving van mogelijke HTTP-statuscodes en hun betekenis, bijvoorbeeld `200`, `400`, `404`, `500`. Elke response kan ook een schema bevatten dat het JSON-antwoord beschrijft.
6. **examples (optioneel)**: Voorbeelden van request en response bodies om de documentatie te verrijken.

Een goed voorbeeld volgens deze conventies:

```python
@app.route('/api/users/<int:user_id>', methods=['GET'])
def get_user(user_id):
    """
    Haalt de gegevens van een specifieke gebruiker op.
    ---
    tags:
      - Users
    parameters:
      - name: user_id
        in: path
        type: integer
        required: true
        description: ID van de gebruiker
    responses:
      200:
        description: Succesvolle respons met gebruikersinformatie
        schema:
          id: User
          properties:
            id:
              type: integer
              description: Unieke ID van de gebruiker
            name:
              type: string
              description: Naam van de gebruiker
      404:
        description: Gebruiker niet gevonden
    """
    # Je implementatie code ...
```

Door deze vaste structuur te volgen, kan **Swagger** of **Flasgger** de docstring automatisch omzetten in duidelijke, interactieve documentatie op de `/apidocs`-pagina, inclusief beschrijvingen, voorbeelddata en invoervelden.


#### 3. Markdown documentatie (zoals deze cursus)

Markdown-documentatie is ideaal voor **readme-bestanden** of **projectwikis**. Je kan hierin beschrijven:

* Wat de API doet
* Welke endpoints beschikbaar zijn
* Voorbeelden van requests en responses
* Foutcodes en uitleg
* Eventuele authenticatievereisten

##### Structuurvoorbeeld

```markdown
# API Documentatie: Gebruikersbeheer

## Base URL
`http://localhost:5000/api`

## Endpoints

### GET /users
Haalt alle gebruikers op.

**Parameters:**
| Naam | Type | Vereist | Beschrijving |
|------|------|----------|---------------|
| pwd  | string | ja | Eenvoudig wachtwoord om toegang te krijgen |

**Response:**
json:

[

  {"id": 1, "name": "John Doe", "email": "john@example.com"},
  {"id": 2, "name": "Jane Smith", "email": "jane@example.com"}

]


**Statuscodes:**

* `200 OK`: Succesvol opgehaald
* `401 Unauthorized`: Onjuiste wachtwoordparameter
* `500 Internal Server Error`: Databasefout

```

Markdown heeft als voordeel dat het eenvoudig leesbaar is en ook op GitHub, GitLab of binnen cursusmateriaal goed weergegeven wordt.

#### Best practices voor goede API-documentatie

1. **Gebruik consistente namen** voor endpoints en parameters.
2. **Beschrijf HTTP-methodes duidelijk** – vermeld steeds of het een `GET`, `POST`, `PUT` of `DELETE` is.
3. **Vermeld de vereiste headers**, vooral als je `Content-Type` of authenticatie gebruikt.
4. **Toon voorbeeldverzoeken en -antwoorden** in JSON-formaat.
5. **Documenteer foutmeldingen** – leg uit wat elke HTTP-statuscode betekent.
6. **Gebruik OpenAPI/Swagger** voor automatische updates van documentatie.
7. **Houd documentatie synchroon met de code** – update deze telkens bij nieuwe endpoints.


#### Extra tools

- **Flasgger**: genereert Swagger-documentatie direct uit docstrings.
- **Flask-RESTX**: maakt REST API-ontwikkeling en documentatie eenvoudiger.
- **Postman / Thunder Client**:kan gebruikt worden om automatisch documentatie te genereren uit je tests.
- **Redoc**: maakt mooie, leesbare documentatiepagina’s vanuit een OpenAPI-specificatie.

### Documenteren van een Laravel REST API met Laravel Scribe

**Laravel Scribe** is een krachtige tool die net zoals Flasgger automatisch REST API-documentatie genereert op basis van annotaties (PHPDoc) en route-informatie in je Laravel-project. Het maakt gebruik van een combinatie van beschrijvingen in je controllers en route informatie om een gebruiksvriendelijke en interactieve documentatiepagina te genereren.


#### Wat is Laravel Scribe

Laravel Scribe leest de metadata van je routes, controllers en request/response voorbeelden en genereert:

* Markdown-documentatie (in `/public/docs`)
* Een interactieve web-UI waarop ontwikkelaars requests kunnen testen

Installatie via Composer:

```bash
composer require --dev knuckleswtf/scribe
```

Daarna publiceer je de configuratie:

```bash
php artisan scribe:install
```

Dit maakt het bestand `config/scribe.php` aan, waar je titel, beschrijving en base URL kunt instellen.

#### Documenteren via PHPDoc-comments

Scribe gebruikt PHPDoc-achtige docblocks boven je controller-methoden. Elke docblock bevat beschrijvingen, parameters, voorbeeldwaarden en mogelijke responses (net zoals we hierboven met Flask gedaan hebben).

De aanbevolen volgorde en conventies:

1. **@group** – groepeert endpoints logisch (zoals `Users` of `Auth`)
2. **Beschrijving** – korte uitleg wat de endpoint doet
3. **@urlParam** – parameters in de URL
4. **@queryParam** – parameters in de querystring
5. **@bodyParam** – parameters in de body van een request (bij POST of PUT)
6. **@response** of **@responseFile** – voorbeelden van mogelijke JSON-responses
7. **@responseField** – uitleg over velden in de response

---

#### Demo 3: `LaravelUserController`

Hieronder staat we onze controller kunt documenteren met Scribe.

<details open>
    <summary><i><b>Klik hier om de code te zien/verbergen voor de `LaravelUserController` met PHPDoc-comments</b></i></summary>
    <p>

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\LaravelUser;

class LaravelUserController extends Controller
{
    /**
     * @group Laravel Users
     * Haal alle gebruikers op.
     *
     * Dit endpoint geeft een lijst terug van alle Laravel-gebruikers als JSON.
     *
     * @queryParam pwd string Vereist. Wachtwoord om toegang te krijgen. Voorbeeld: mypassword
     * @response 200 scenario="Succesvol" {"id": 1, "name": "John Doe", "email": "john@example.com"}
     * @response 403 scenario="Verkeerd wachtwoord" {"message": "Wrong password mate"}
     */
    public function getLaravelUsers(Request $request)
    {
        $pwd = $request->query('pwd');
        if ($pwd == 'mypassword') {
            $laravelUsers = LaravelUser::all();
            return response()->json($laravelUsers, 200);
        } else {
            return response()->json(['message' => 'Wrong password mate'], 403);
        }
    }

    /**
     * @group Laravel Users
     * Haal een specifieke gebruiker op.
     *
     * Geeft de gegevens van een gebruiker terug op basis van zijn ID.
     *
     * @urlParam id integer Vereist. De ID van de gebruiker. Example: 1
     * @response 200 {"id": 1, "name": "John Doe", "email": "john@example.com"}
     * @response 404 {"message": "User not found"}
     */
    public function getLaravelUser($id)
    {
        $laravelUser = LaravelUser::find($id);
        if ($laravelUser) {
            return response()->json($laravelUser, 200);
        } else {
            return response()->json(['message' => 'User not found'], 404);
        }
    }

    /**
     * @group Laravel Users
     * Maak een nieuwe gebruiker aan.
     *
     * @bodyParam name string Vereist. De naam van de gebruiker. Example: John Doe
     * @bodyParam email string Vereist. Het e-mailadres van de gebruiker. Example: john@example.com
     * @response 201 {"message": "User created successfully"}
     * @response 400 {"error": "Name and email required"}
     * @response 500 {"error": "Database error"}
     */
    public function createLaravelUser(Request $request)
    {
        $name = $request->input('name');
        $email = $request->input('email');

        if (!$name || !$email) {
            return response()->json(['error' => 'Name and email required'], 400);
        }

        try {
            $laravelUser = new LaravelUser();
            $laravelUser->name = $name;
            $laravelUser->email = $email;
            $laravelUser->save();

            return response()->json(['message' => 'User created successfully'], 201);
        } catch (\Exception $e) {
            return response()->json(['error' => $e->getMessage()], 500);
        }
    }

    /**
     * @group Laravel Users
     * Verwijder een gebruiker.
     *
     * @urlParam id integer Vereist. De ID van de gebruiker. Example: 3
     * @response 200 {"message": "User deleted successfully"}
     * @response 404 {"message": "User not found"}
     */
    public function deleteLaravelUser($id)
    {
        $laravelUser = LaravelUser::find($id);
        if (!$laravelUser) {
            return response()->json(['message' => 'User not found'], 404);
        }

        $laravelUser->delete();
        return response()->json(['message' => 'User deleted successfully'], 200);
    }
}
```

</p>
</details>

#### Genereren van documentatie

Als je de docblocks hebt toegevoegd, kun je de documentatie genereren met:

```bash
php artisan scribe:generate
```

De documentatie wordt beschikbaar gemaakt op:

```
http://localhost/docs
```

Daar vind je een interactieve interface waarin je requests kunt uitvoeren, voorbeelden kunt bekijken en parameters kunt testen.

---

#### Samenvatting en Best practices

* **Gebruik consistente @group-tags** voor duidelijke indeling.
* **Schrijf korte maar beschrijvende uitleg** boven elke route.
* **Gebruik voorbeelddata** via `Example:` om gegenereerde UI beter te vullen.
* **Documenteer alle statuscodes** die je gebruikt (`200`, `400`, `403`, `404`, `500`).
* **Her-genereer documentatie** altijd wanneer je routes of responses aanpast.

Met deze aanpak genereert Laravel Scribe automatisch professioneel ogende documentatie voor jouw REST API.
