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

<details open>
    <summary><i><b>Klik hier om de code te zien/verbergen voor de volledig geannoteerde Flask app</b></i></summary>
    <p>

```python
from flask import Flask, request, jsonify
from flask_cors import CORS
import pymysql
import os
from flasgger import Swagger

app = Flask(__name__)
CORS(app)
swagger = Swagger(app)


def get_db_connection():
    """
    Maak een verbinding met de MySQL-database.

    De connectieparameters worden opgehaald via omgevingsvariabelen:
    - DB_HOST (default: 'db')
    - DB_USER (default: 'root')
    - DB_PASSWORD (default: 'root')
    - DB_NAME (default: 'restapi')
    """
    return pymysql.connect(
        host=os.getenv('DB_HOST', 'db'),
        user=os.getenv('DB_USER', 'root'),
        password=os.getenv('DB_PASSWORD', 'root'),
        database=os.getenv('DB_NAME', 'restapi'),
        charset='utf8mb4',
        cursorclass=pymysql.cursors.DictCursor
    )


@app.route('/api/users', methods=['GET'])
def get_users():
    """
    Haal alle gebruikers op.

    ---
    parameters:
      - name: pwd
        in: query
        type: string
        required: true
        description: Eenvoudig wachtwoord om toegang te krijgen (demodoeleinden)
    responses:
      200:
        description: Lijst van gebruikers
        schema:
          type: array
          items:
            type: object
            properties:
              id:
                type: integer
              name:
                type: string
              email:
                type: string
      401:
        description: Ongeldig wachtwoord
    """
    pwd = request.args.get("pwd")
    if pwd != "mypassword":
        return jsonify({"error": "Invalid password"}), 401

    db = get_db_connection()
    try:
        cursor = db.cursor()
        cursor.execute("SELECT * FROM users")
        users = cursor.fetchall()
        return jsonify(users), 200
    except Exception as e:
        return jsonify({"error": str(e)}), 500
    finally:
        db.close()


@app.route('/api/users/<int:user_id>', methods=['GET'])
def get_user(user_id):
    """
    Haal een specifieke gebruiker op via ID.

    ---
    parameters:
      - name: user_id
        in: path
        type: integer
        required: true
        description: ID van de gebruiker
    responses:
      200:
        description: Gevonden gebruiker
        schema:
          type: object
          properties:
            id:
              type: integer
            name:
              type: string
            email:
              type: string
      404:
        description: Gebruiker niet gevonden
    """
    db = get_db_connection()
    try:
        cursor = db.cursor()
        cursor.execute("SELECT * FROM users WHERE id = %s", user_id)
        user = cursor.fetchone()
        if user:
            return jsonify(user), 200
        return jsonify({"error": "User not found"}), 404
    except Exception as e:
        return jsonify({"error": str(e)}), 500
    finally:
        db.close()


@app.route('/api/users', methods=['POST'])
def create_user():
    """
    Maak een nieuwe gebruiker aan.

    ---
    parameters:
      - in: body
        name: body
        required: true
        schema:
          type: object
          required:
            - name
            - email
          properties:
            name:
              type: string
              example: "Alice"
            email:
              type: string
              example: "alice@example.com"
    responses:
      201:
        description: Gebruiker succesvol aangemaakt
      400:
        description: Ongeldige invoer
    """
    data = request.get_json()
    name = data.get('name')
    email = data.get('email')
    if not name or not email:
        return jsonify({"error": "Name and email required"}), 400

    db = get_db_connection()
    try:
        cursor = db.cursor()
        sql = "INSERT INTO users (name, email) VALUES (%s, %s)"
        cursor.execute(sql, (name, email))
        db.commit()
        return jsonify({"message": "User created successfully"}), 201
    except Exception as e:
        db.rollback()
        return jsonify({"error": str(e)}), 500
    finally:
        db.close()


@app.route('/api/users/<int:user_id>', methods=['PUT'])
def update_user(user_id):
    """
    Werk een bestaande gebruiker bij.

    ---
    parameters:
      - name: user_id
        in: path
        type: integer
        required: true
        description: ID van de gebruiker
      - in: body
        name: body
        required: true
        schema:
          type: object
          required:
            - name
            - email
          properties:
            name:
              type: string
              example: "Bob"
            email:
              type: string
              example: "bob@example.com"
    responses:
      200:
        description: Gebruiker succesvol bijgewerkt
      404:
        description: Gebruiker niet gevonden
      400:
        description: Ongeldige invoer
    """
    data = request.get_json()
    name = data.get('name')
    email = data.get('email')
    if not name or not email:
        return jsonify({"error": "Name and email required"}), 400

    db = get_db_connection()
    try:
        cursor = db.cursor()
        cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))
        user = cursor.fetchone()
        if not user:
            return jsonify({"error": "User not found"}), 404
        cursor.execute("UPDATE users SET name = %s, email = %s WHERE id = %s", (name, email, user_id))
        db.commit()
        return jsonify({"message": "User updated successfully"}), 200
    except Exception as e:
        db.rollback()
        return jsonify({"error": str(e)}), 500
    finally:
        db.close()


@app.route('/api/users/<int:user_id>', methods=['DELETE'])
def delete_user(user_id):
    """
    Verwijder een gebruiker via ID.

    ---
    parameters:
      - name: user_id
        in: path
        type: integer
        required: true
        description: ID van de te verwijderen gebruiker
    responses:
      200:
        description: Gebruiker succesvol verwijderd
      404:
        description: Gebruiker niet gevonden
    """
    db = get_db_connection()
    try:
        cursor = db.cursor()
        cursor.execute("DELETE FROM users WHERE id = %s", user_id)
        db.commit()
        if cursor.rowcount == 0:
            return jsonify({"error": "User not found"}), 404
        return jsonify({"message": "User deleted successfully"}), 200
    except Exception as e:
        return jsonify({"error": str(e)}), 500
    finally:
        db.close()


if __name__ == '__main__':
    app.run(host='0.0.0.0', debug=True)
```

</p>
</details>

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


#### 3. Markdown documentatie

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

_Uitvoeren in je projectmap!_

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
http://localhost:<portnr>/docs
```

Daar vind je een interactieve interface waarin je requests kunt uitvoeren, voorbeelden kunt bekijken en parameters kunt testen. (_het interactieve deel werkt voorlopig niet._)

<!-- **Standaard gebruikt scribe voor zijn testfuncties op de /docs pagina de url localhost i.p.v. localhost:<portnr>. Om zelf de base_url te kunnen instellen maken we de file `config/scribe.php` aan:**

```php
<?php

return [
    /*
     * The HTML <title> for the generated documentation, and the name of the generated Postman collection.
     */
    'title' => 'Laravel API Documentation',

    /*
     * A short description of your API.
     */
    'description' => 'Laravel REST API Documentation',

    /*
     * The base URL displayed in the docs. If this is empty, Scribe will use the value of config('app.url').
     */
    'base_url' => 'http://localhost:8000',

    /*
     * Tell Scribe what routes you want to have documented.
     * Each group contains rules defining which routes should be included ('match')
     * and rules defining which routes should be excluded ('exclude').
     * A route must fulfill ALL match conditions and NONE of the exclude conditions to be included.
     */
    'routes' => [
        [
            /*
             * Specify conditions to determine what routes will be parsed.
             */
            'match' => [
                /*
                 * Match only routes whose paths match this pattern (use * as a wildcard to match any characters).
                 */
                'prefixes' => ['api/*'],

                /*
                 * Match only routes whose names match this pattern (use * as a wildcard to match any characters).
                 */
                // 'names' => ['users.*'],

                /*
                 * Match only routes registered under this version. This option is ignored for Laravel router.
                 * Note that wildcards are not supported.
                 */
                // 'versions' => ['v1'],
            ],

            /*
             * Include these routes even if they did not match the rules above.
             * The route must be referenced by name here (wildcards are supported).
             */
            'include' => [
                // 'users.index', 'healthcheck*'
            ],

            /*
             * Exclude these routes even if they matched the rules above.
             * The route must be referenced by name here (wildcards are supported).
             */
            'exclude' => [
                // 'users.create', 'admin.*'
            ],
        ],
    ],

    /*
     * The type of documentation output to generate.
     * - "static" will generate a static HTMl page in the /public/docs folder
     * - "laravel" will generate the documentation as a Blade view, so you can add routing and authentication.
     */
    'type' => 'static',

    /*
     * Settings for `static` type output.
     */
    'static' => [
        /*
         * HTML documentation, assets and Postman collection will be generated to this folder.
         * Source Markdown will still be in resources/docs.
         */
        'output_path' => 'public/docs',
    ],

    /*
     * Settings for `laravel` type output.
     */
    'laravel' => [
        /*
         * Whether to automatically create a docs endpoint for you to view your generated docs.
         * If this is false, you can still set up routing manually.
         */
        'add_routes' => true,

        /*
         * URL path to use for the docs endpoint (if `add_routes` is true).
         * By default, `/docs` will be used.
         */
        'docs_url' => '/docs',

        /*
         * Directory within `public/` in which to store CSS and JS assets.
         * By default, assets are stored in `public/docs`.
         */
        'assets_directory' => 'docs',

        /*
         * Middleware to attach to the docs endpoint (if `add_routes` is true).
         */
        'middleware' => [],
    ],

    /*
     * How is your API authenticated? This information will be used in the displayed docs, generated examples and response calls.
     * Available options: *bearer*, *basic*, *header* (will use the specified key and source), or *query* (will use the specified key and source)
     */
    'auth' => [
        /*
         * Set this to true if any endpoints in your API uses authentication.
         */
        'enabled' => false,

        /*
         * Set this to true if your API should be tested with the auth parameters supplied.
         */
        'default' => false,

        /*
         * The location of the auth key. Can be 'query', 'body', 'basic', 'bearer', or 'header'.
         */
        'in' => 'bearer',

        /*
         * The name of the parameter (if location is query or body) or header (if location is header).
         */
        'name' => 'token',

        /*
         * The value of the parameter to be used by Scribe to authenticate API requests.
         */
        'use_value' => env('SCRIBE_AUTH_KEY'),

        /*
         * Placeholder your users will see for the auth parameter in the example requests.
         * Set this to null if you want Scribe to use a generated value as placeholder instead.
         */
        'placeholder' => '{YOUR_AUTH_KEY}',

        /*
         * Any extra authentication-related info for your users. For instance, you can describe how to find or generate their auth credentials.
         * Markdown and HTML are supported.
         */
        'extra_info' => 'You can retrieve your token by visiting your dashboard and clicking <b>Generate API token</b>.',
    ],

    /*
     * Text to place in the "Introduction" section, right after the `description`. Markdown and HTML are supported.
     */
    'intro_text' => <<<INTRO
This documentation aims to provide all the information you need to work with our API.

<aside>As you scroll, you'll see code examples for working with the API in different programming languages in the dark area to the right (or as part of the content on mobile).
These examples are automatically generated from the API documentation, so they're always up to date.</aside>
INTRO,

    /*
     * Example requests for each endpoint will be shown in each of these languages.
     * Supported options are: bash, javascript, php, python
     * Defaults to 'bash' and 'javascript'
     */
    'example_languages' => [
        'bash',
        'javascript',
        'php',
        'python',
    ],

    /*
     * Generate a Postman collection (v2.1.0) in addition to HTML docs.
     * For 'static' docs, the collection will be generated to public/docs/collection.json.
     * For 'laravel' docs, it will be generated to storage/app/scribe/collection.json.
     * Setting this to false will disable Postman collection generation.
     */
    'postman' => [
        'enabled' => true,
        'overrides' => [
            // 'info.version' => '2.0.0',
        ],
    ],

    /*
     * Generate an OpenAPI spec (3.0.1) in addition to docs.
     * For 'static' docs, the collection will be generated to public/docs/openapi.yaml.
     * For 'laravel' docs, it will be generated to storage/app/scribe/openapi.yaml.
     * Setting this to false will disable OpenAPI spec generation.
     */
    'openapi' => [
        'enabled' => true,
        'overrides' => [
            // 'info.version' => '2.0.0',
        ],
    ],

    /*
     * Customize the "Try It Out" feature by setting the URL and headers Scribe will use when making the test requests.
     * The base URL displayed in the docs is set by `base_url` above.
     */
    'try_it_out' => [
        /*
         * Add a "Try It Out" button to your endpoints so consumers can test endpoints right from their browser.
         * Don't forget to enable CORS headers for your endpoints.
         */
        'enabled' => true,

        /*
         * The base URL for the endpoint tester. If you set this to null, Scribe will use the value of 'base_url' above.
         */
        'base_url' => 'http://localhost:8000',

        /*
         * Specify the headers that will be sent with the API test requests.
         */
        'headers' => [
            // 'Api-Version' => 'v1',
        ],
    ],

    /*
     * How much of your routes' parameters, responses, and examples should Scribe try to figure out without you explicitly telling it what they are?
     *
     * By default, Scribe will extract parameters from FormRequests, generate examples and responses by making database queries, and more.
     * However, if you're using other ways to validate parameters or return data, you may need to explicitly describe them.
     * Some of these strategies are only useful in development (when you have a seeded test database for example), so you may not want to use them in production.
     *
     * To avoid guessing (and speed up generation), you can disable any of the strategies below by removing or commenting them out.
     * You can also add a custom strategy if needed.
     */
    'strategies' => [
        'metadata' => [
            \Knuckles\Scribe\Extracting\Strategies\Metadata\GetFromDocBlocks::class,
            \Knuckles\Scribe\Extracting\Strategies\Metadata\GetFromMetadataAttributes::class,
        ],
        'urlParameters' => [
            \Knuckles\Scribe\Extracting\Strategies\UrlParameters\GetFromLaravelAPI::class,
            \Knuckles\Scribe\Extracting\Strategies\UrlParameters\GetFromLumenAPI::class,
            \Knuckles\Scribe\Extracting\Strategies\UrlParameters\GetFromUrlParamAttribute::class,
        ],
        'queryParameters' => [
            \Knuckles\Scribe\Extracting\Strategies\QueryParameters\GetFromFormRequest::class,
            \Knuckles\Scribe\Extracting\Strategies\QueryParameters\GetFromInlineValidator::class,
            \Knuckles\Scribe\Extracting\Strategies\QueryParameters\GetFromQueryParamAttribute::class,
        ],
        'headers' => [
            \Knuckles\Scribe\Extracting\Strategies\Headers\GetFromRouteRules::class,
            \Knuckles\Scribe\Extracting\Strategies\Headers\GetFromHeaderAttribute::class,
        ],
        'bodyParameters' => [
            \Knuckles\Scribe\Extracting\Strategies\BodyParameters\GetFromFormRequest::class,
            \Knuckles\Scribe\Extracting\Strategies\BodyParameters\GetFromInlineValidator::class,
            \Knuckles\Scribe\Extracting\Strategies\BodyParameters\GetFromBodyParamAttribute::class,
        ],
        'responses' => [
            \Knuckles\Scribe\Extracting\Strategies\Responses\UseResponseAttributes::class,
            \Knuckles\Scribe\Extracting\Strategies\Responses\UseApiResourceTags::class,
            \Knuckles\Scribe\Extracting\Strategies\Responses\UseTransformerTags::class,
            \Knuckles\Scribe\Extracting\Strategies\Responses\ResponseCalls::class,
        ],
        'responseFields' => [
            \Knuckles\Scribe\Extracting\Strategies\ResponseFields\GetFromResponseFieldAttribute::class,
        ],
    ],

    /*
     * Configure how responses are processed.
     */
    'responses' => [
        /*
         * If enabled, Scribe will try to generate responses by making actual HTTP requests to your routes.
         * This can be slow, and requires your app to be running, but will give you accurate responses.
         */
        'calls' => [
            'enabled' => env('SCRIBE_MAKE_CALLS', false),

            /*
             * Configure which URLs will be called.
             * Scribe will only attempt calls if your app uses the default base URL (127.0.0.1:8000 or localhost:8000).
             * If your app uses a different URL, set the base URL here.
             */
            'base_urls' => [
                'http://localhost:8000',
                'http://127.0.0.1:8000',
            ],

            /*
             * HTTP methods that can be called. Requests that destructively modify your database (like POST, PUT, and DELETE) can be dangerous to run automatically.
             * By default, Scribe will only attempt GET requests.
             */
            'methods' => ['GET'],

            /*
             * Headers to send when making "response calls".
             */
            'headers' => [
                // 'Authorization' => 'Bearer {token}',
            ],

            /*
             * Configure which routes will have response calls made.
             * Routes can be included by name, path, or method.
             * By default, no routes are excluded.
             */
            'config' => [
                // 'routes.0.include' => ['users.*'],
                // 'routes.0.exclude' => ['users.create', 'users.store'],
            ],
        ],

        /*
         * For response calls, or when you use some specific strategies, Scribe might need to generate arbitrary model instances.
         * By default, Scribe will try to generate these intelligently. If you'd like to provide custom instances for any models, you may specify them here.
         */
        'use_faker_for_nullable_fields' => true,
    ],

    /*
     * The HTML theme for your docs (see https://scribe.knuckles.wtf/laravel/themes/).
     */
    'theme' => 'default',

    /*
     * Any custom CSS and JavaScript assets for your docs. The files must be placed in your project's public/ directory.
     * For example, if you set this to:
     * [
     *     'css' => ['css/theme_overrides.css'],
     *     'js' => ['js/custom.js'],
     * ]
     * 
     * Then the 'theme_overrides.css' file should be at public/css/theme_overrides.css, and 'custom.js' at public/js/custom.js.
     * 
     * In your CSS and JavaScript, you can also reference files under public/vendor/scribe. 
     * For example, in your CSS, you can import the main stylesheet with: @import url(../vendor/scribe/css/theme-default.style.css);
     */
    'assets' => [
        'css' => [],
        'js' => [],
    ],
];
``` -->

#### Samenvatting en Best practices

Algemeen:
* **Gebruik consistente namen** voor endpoints en parameters.
* **Beschrijf HTTP-methodes duidelijk**: vermeld steeds of het een `GET`, `POST`, `PUT` of `DELETE` is.
* **Vermeld de vereiste headers**, vooral als je `Content-Type` of authenticatie gebruikt.
* **Gebruik voorbeelddata** via `Example:` om gegenereerde UI beter te vullen.
* **Documenteer alle statuscodes** die je gebruikt (`200`, `400`, `403`, `404`, `500`).
* **Documenteer dus ook foutmeldingen**: leg uit wat elke HTTP-statuscode betekent.

Flask:
* **Toon voorbeeldverzoeken en -antwoorden** in JSON-formaat.
* **Gebruik OpenAPI/Swagger** voor automatische updates van documentatie.
* **Houd documentatie synchroon met de code**: swagger module houdt dit normaal in de gaten.

Laravel:
* **Gebruik consistente @group-tags** voor duidelijke indeling.
* **Schrijf korte maar beschrijvende uitleg** boven elke route.
* **Houd documentatie synchroon met de code**: Her-genereer documentatie altijd wanneer je routes of responses aanpast.