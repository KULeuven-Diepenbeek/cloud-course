---
title: "REST api flask"
weight: 5
author: Arne Duyver
draft: false
---

## Demo 1: Simpele REST api met Flask (Python) en MariaDB

We maken een voorbeeld van een eenvoudige Flask-applicatie die CORS en PyMySQL gebruikt om een REST API te creëren met volledige CRUD-functionaliteit. 

Zorg ervoor dat je de vereiste pakketten installeert:

```bash
pip install Flask flask-cors pymysql
```

_**Als je de demo folder gebruikt uit de repository, dan kan je gewoon de docker-compose.yml gebruiken om m.b.v. containers de Flask api te starten (met de correcte modules al geïnstalleerd) en met een MariaDB database als onderdeel van dezelfde service!**_

## app.py
<details open>
    <summary><i><b>Klik hier om de code te zien/verbergen voor `app.py`</b></i></summary>
    <p>

```python
from flask import Flask, render_template_string, request, jsonify
from flask_cors import CORS
import pymysql
import os

app = Flask(__name__)
# Met de CORS functie: cross-origin resource sharing laten we toe dat een andere website toegang krijgt tot deze endpoints
# De requests mogen van een andere "origin" komen. Als deze website www.mijnwebsite.com heet dan laten we nu dus toe dat niet alleen een request van www.mijnwebsite.com naar www.mijnwebsite.com/api/users werkt, 
# maar dat een request van www.mijnanderewebsite.com naar www.mijnwebsite.com/api/users ook zal werken
CORS(app)

# Database connection
# We gebruiken hier de ENVIRONMENT variables die we definieren in de docker-compose file om een connectie te kunnen leggen met de database
# gebruik: de tweede parameter van getenv is een default waarde in geval dat de environment variable van de eerste parameter niet bestaat
def get_db_connection():
    return pymysql.connect( 
        host=os.getenv('DB_HOST', 'db'), # db is de naam van de service zoals we die gedefinieerd hebben in de docker-compose file
        user=os.getenv('DB_USER', 'root'),
        password=os.getenv('DB_PASSWORD', 'root'),
        database=os.getenv('DB_NAME', 'restapi'),
        charset='utf8mb4',
        cursorclass=pymysql.cursors.DictCursor
    )

# DO REST API STUFF

# READ - vraag een lijst op van alle users
# we gebruiken het endpoint /api/users. Elk http-get request naar www.mijnwebsite.be/api/users zal deze lijst in json formaat terugkrijgen
@app.route('/api/users', methods=['GET'])
def get_users():
    # We kunnen het endpoint beveiligen met een simpel wachtwoord.
    pwd = request.args.get("pwd")
    # de arguments die herkent worden door request.args moeten er als volgt uit zien:
    # www.mijnwebsite.be/api/users?parameter1=waarde1&parameter2=waarde2
    # vb: www.mijnwebsite.be/api/users?pwd=mypassword
    if pwd == "mypassword":
        db = get_db_connection()
        try:
            cursor = db.cursor(pymysql.cursors.DictCursor)
            cursor.execute("SELECT * FROM users")
            users = cursor.fetchall()
            # de userslijst zit nu in de variabele users. We moeten deze lijst nog omvormen tot een JSON object m.b.v. jsonify
            return jsonify(users), 200
            # Het nummer achter de return waarde specifieert dat er geen errors zijn opgedoken tijdens het behandelen van dit HTTP GET request
        except Exception as e:
            return jsonify({"error":str(e)}), 500
        finally:
            db.close()
    else:
        return "Wrong password mate"
    

# READ - vraag een specifieke user op basis van de id in de tabel
# we gebruiken het endpoint /api/users/<int>. Elk http-get request naar www.mijnwebsite.be/api/users/<int> zal de gebruiker met id=<int> terugkrijgen als een JSON object
@app.route('/api/users/<int:user_id>', methods=['GET'])
# We specifieren dat elke integer als een geldig endpoint beschouwd moet worden en dat we dat integer opslaan in de variabele user_id
def get_user(user_id):
    db = get_db_connection()
    try:
        cursor = db.cursor(pymysql.cursors.DictCursor)
        cursor.execute("SELECT * FROM users WHERE id = %s", user_id)
        user = cursor.fetchone()
        # We geven de user enkel terug als die ook echt bestaat, de user_id kan namelijk fout zijn
        if user:
            return jsonify(user), 200
        else:
            return jsonify({"message":"User not found"}), 500
    
    except Exception as e:
        return jsonify({"error":str(e)}), 500
    finally:
        db.close()

# DELETE - delete een specifieke user op basis van de id in de tabel
# we gebruiken het endpoint /api/users/<int>. Elk http-get request naar www.mijnwebsite.be/api/users/<int> zal de gebruiker met id=<int> deleten
@app.route('/api/users/<int:user_id>', methods=['DELETE'])
# We specifieren dat elke integer als een geldig endpoint beschouwd moet worden en dat we dat integer opslaan in de variabele user_id
def delete_user(user_id):
    db = get_db_connection()
    try:
        cursor = db.cursor(pymysql.cursors.DictCursor)
        cursor.execute("DELETE FROM users WHERE id = %s", user_id)
        # aangezien we niet enkel uitlezen maar de database ook effectief wijzigen, is het belangrijk de wijziging ook te committen
        db.commit()
        # enkel als er effectief iets gewijzigd is, is er een user gedelete
        if cursor.rowcount == 0:
            return jsonify({"message":"User not found"}), 404
        return jsonify({"message":"User deleted successfully"}), 200
    except Exception as e:
        return jsonify({"error":str(e)}), 500
    finally:
        db.close()

# CREATE - maak een user aan op basis van het meegegeven JSON object
@app.route('/api/users', methods=['POST'])
def create_user():
    db = get_db_connection()
    # de data die doorgestuurd wordt bevind zich in de body van het POST-request. In dit geval verwachten we een JSON formaat
    data = request.get_json()
    # We gaan ervan uit dat het JSON object deze waarden bevat
    name = data.get('name')
    email = data.get('email')
    # indien het JSON object niet de juiste informatie bevat geven we dit terug in de vorm van een error
    if not name or not email:
        return jsonify({"error": "Name and email required"}), 400
    try:
        # Indien het JSON object de correcte informatie bevat, kunnen we het gebruiken om een user aan te maken en in de database op te slaan
        cursor = db.cursor()
        sql = "INSERT INTO users (name, email) VALUES (%s, %s)"
        cursor.execute(sql, (name, email))
        # aangezien we niet enkel uitlezen maar de database ook effectief wijzigen, is het belangrijk de wijziging ook te committen
        db.commit()
        return jsonify({"message": "User created successfully"}), 201
    except Exception as e:
        db.rollback()
        return jsonify({"error": str(e)}), 500
    finally:
        db.close()

# UPDATE - werk een bestaande user bij op basis van het meegegeven JSON object
@app.route('/api/users/<int:id>', methods=['PUT'])
def update_user(id):
    db = get_db_connection()
    # De data die doorgestuurd wordt bevindt zich in de body van het PUT-request. In dit geval verwachten we een JSON-formaat
    data = request.get_json()
    # We gaan ervan uit dat het JSON-object deze waarden bevat
    name = data.get('name')
    email = data.get('email')
    # Controleer of de juiste gegevens zijn meegegeven in de JSON
    if not name or not email:
        return jsonify({"error": "Name and email required"}), 400
    try:
        # Check of de user met de gegeven id bestaat
        cursor = db.cursor()
        cursor.execute("SELECT * FROM users WHERE id = %s", (id,))
        user = cursor.fetchone()
        if not user:
            return jsonify({"error": "User not found"}), 404
        # Indien de user bestaat, werk de gegevens bij
        sql = "UPDATE users SET name = %s, email = %s WHERE id = %s"
        cursor.execute(sql, (name, email, id))
        # Vergeet niet om de wijziging te committen
        db.commit()
        return jsonify({"message": "User updated successfully"}), 200
    except Exception as e:
        db.rollback()
        return jsonify({"error": str(e)}), 500'

if __name__ == '__main__':
    # start de flask server op: de host moet 0.0.0.0 zijn om correct te werken met docker
    app.run(host='0.0.0.0', debug=True)
```

</p>
</details>

## Uitleg:
**Flask**: Biedt het webframework voor het afhandelen van routes.
**CORS**: Staat cross-origin-aanvragen toe, wat handig is als de frontend afzonderlijk wordt gehost.
**PyMySQL**: maakt verbinding met de MySQL-database.
**CRUD-operaties**:
  - **Aanmaken**: `POST api/users` maakt een nieuwe gebruiker aan.
  - **Lezen**: `GET api/users?pwd=mypassword` haalt alle gebruikers op, en GET /users/<id> haalt een specifieke gebruiker op.
  - **Update**: `PUT api/users/<id>` werkt een specifieke gebruiker bij op ID.
  - **Verwijderen**: `DELETE api/users/<id>` verwijdert een specifieke gebruiker op ID.



## De database

<details open>
<summary><b>Klik hier om de code te zien/verbergen voor SQL-code initialiseren database <code>restapi</code> </b></summary>

```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO users (name, email) VALUES 
('John Doe', 'john.doe@example.com'),
('Jane Smith', 'jane.smith@example.com'),
('Alice Johnson', 'alice.johnson@example.com'),
('Bob Brown', 'bob.brown@example.com'),
('Charlie Green', 'charlie.green@example.com'),
('Emily Davis', 'emily.davis@example.com'),
('Michael Wilson', 'michael.wilson@example.com'),
('Sarah White', 'sarah.white@example.com'),
('David King', 'david.king@example.com'),
('Laura Scott', 'laura.scott@example.com');
```

</details>

## REST API testen met Thunder Client

Nu onze Flask REST API in een Docker container draait, kunnen we deze testen vanuit VS Code met de Thunder Client extensie. Thunder Client is een lightweight REST API client die direct in VS Code geïntegreerd is.

### Voorbereiding

1. **Installeer Thunder Client extensie** in VSCode: Zoek naar "Thunder Client" en installeer de extensie van Ranga Vadhineni

2. **Zorg dat je Docker container draait** op poort 5000:

3. **Open Thunder Client** via het zijpaneel in VSCode of `Ctrl+Shift+P` -> "Thunder Client"

### Handmatige tests uitvoeren

#### GET - Alle gebruikers ophalen
1. Klik op "New Request" in Thunder Client
2. Stel de request in:
   - **Method**: GET
   - **URL**: `http://localhost:5000/api/users?pwd=mypassword`
   - **Headers**: Content-Type: application/json (optioneel voor GET)
3. Klik "Send"
4. **Verwacht resultaat**: JSON array met alle gebruikers uit de database

*alternateief: `curl`*
```bash
curl -X GET "http://localhost:5000/api/users?pwd=mypassword" -H "Content-Type: application/json"
```

#### GET - Specifieke gebruiker ophalen
1. Nieuwe request:
   - **Method**: GET
   - **URL**: `http://localhost:5000/api/users/1`
2. **Verwacht resultaat**: JSON object van gebruiker met ID 1

*alternateief: `curl`*
```bash
curl -X GET "http://localhost:5000/api/users/1" -H "Content-Type: application/json"
```

#### POST - Nieuwe gebruiker aanmaken
1. Nieuwe request:
   - **Method**: POST
   - **URL**: `http://localhost:5000/api/users`
   - **Headers**: Content-Type: application/json
   - **Body** (JSON):
     ```json
     {
       "name": "Test User",
       "email": "test@example.com"
     }
     ```
2. **Verwacht resultaat**: Bevestiging dat gebruiker is aangemaakt

*alternateief: `curl`*
```bash
curl -X POST "http://localhost:5000/api/users" \
-H "Content-Type: application/json" \
-d '{
  "name": "Test User",
  "email": "test@example.com"
}'
```

#### PUT - Gebruiker bijwerken
1. Nieuwe request:
   - **Method**: PUT
   - **URL**: `http://localhost:5000/api/users/1`
   - **Headers**: Content-Type: application/json
   - **Body** (JSON):
     ```json
     {
       "name": "Updated User",
       "email": "updated@example.com"
     }
     ```

*alternateief: `curl`*
```bash
curl -X PUT "http://localhost:5000/api/users/1" \
-H "Content-Type: application/json" \
-d '{
  "name": "Updated User",
  "email": "updated@example.com"
}'
```

#### DELETE - Gebruiker verwijderen
1. Nieuwe request:
   - **Method**: DELETE
   - **URL**: `http://localhost:5000/api/users/1`

*alternateief: `curl`*
```bash
curl -X DELETE "http://localhost:5000/api/users/1" -H "Content-Type: application/json"
```

<!-- #### Automatiseren met Thunder Client Collections

Voor efficiënt testen kunnen we een **Collection** aanmaken met voorgemaakte tests:

##### Collection aanmaken en exporteren

1. **Maak een nieuwe Collection**:
   - Klik op "Collections" tab in Thunder Client
   - Klik "New Collection" 
   - Naam: "Flask REST API Tests"

2. **Voeg alle requests toe aan de collection**:
   - Bij elke request: klik op "Save" -> selecteer de collection
   - Geef elke request een duidelijke naam zoals "Get All Users", "Create User", etc.

3. **Exporteer de collection**:
   - Rechtsklik op de collection -> "Export"
   - Sla op als `flask-api-tests.json` in je projectfolder

##### Voorgemaakte test collection gebruiken

<details open>
<summary><b>Klik hier voor de complete Thunder Client collection (flask-api-tests.json)</b></summary>

```json
{
  "client": "Thunder Client",
  "collectionName": "Flask REST API Tests",
  "dateExported": "2025-01-XX",
  "version": "1.1",
  "folders": [],
  "requests": [
    {
      "name": "Get All Users",
      "url": "http://localhost:5000/api/users?pwd=mypassword",
      "method": "GET",
      "headers": [
        {
          "name": "Content-Type",
          "value": "application/json"
        }
      ],
      "tests": [
        {
          "type": "res-code",
          "value": "200"
        },
        {
          "type": "res-header",
          "key": "content-type",
          "value": "application/json"
        }
      ]
    },
    {
      "name": "Get User by ID",
      "url": "http://localhost:5000/api/users/1",
      "method": "GET",
      "headers": [
        {
          "name": "Content-Type",
          "value": "application/json"
        }
      ],
      "tests": [
        {
          "type": "res-code",
          "value": "200"
        }
      ]
    },
    {
      "name": "Create New User",
      "url": "http://localhost:5000/api/users",
      "method": "POST",
      "headers": [
        {
          "name": "Content-Type",
          "value": "application/json"
        }
      ],
      "body": {
        "type": "json",
        "raw": "{\n  \"name\": \"Thunder Client User\",\n  \"email\": \"thunder@example.com\"\n}"
      },
      "tests": [
        {
          "type": "res-code",
          "value": "201"
        }
      ]
    },
    {
      "name": "Update User",
      "url": "http://localhost:5000/api/users/2",
      "method": "PUT",
      "headers": [
        {
          "name": "Content-Type",
          "value": "application/json"
        }
      ],
      "body": {
        "type": "json",
        "raw": "{\n  \"name\": \"Updated Thunder User\",\n  \"email\": \"updated.thunder@example.com\"\n}"
      },
      "tests": [
        {
          "type": "res-code",
          "value": "200"
        }
      ]
    },
    {
      "name": "Delete User",
      "url": "http://localhost:5000/api/users/2",
      "method": "DELETE",
      "headers": [
        {
          "name": "Content-Type",
          "value": "application/json"
        }
      ],
      "tests": [
        {
          "type": "res-code",
          "value": "200"
        }
      ]
    }
  ]
}
```

</details>

#### Collection importeren en gebruiken

1. **Importeer de collection**:
   - Thunder Client -> Collections tab
   - Klik "Import" -> selecteer `flask-api-tests.json`

2. **Tests uitvoeren**:
   - **Individuele test**: Klik op een request en "Send"
   - **Hele collection**: Rechtsklik op collection -> "Run All"

3. **Test resultaten bekijken**:
   - Elke request heeft automatische validatie (status codes)
   - Groene vinkjes = geslaagde tests
   - Rode kruisjes = gefaalde tests
-->
### Troubleshooting veelvoorkomende problemen

- **Connection refused errors**
    - Probleem: `ECONNREFUSED localhost:5000`
    - Oplossing: Controleer of Docker container draait: `docker ps`
- **CORS errors**  
    - Probleem: Cross-origin blocked
    - Oplossing: check of CORS is ingeschakeld in de Flask app met `CORS(app)`
- **500 Internal Server Error**
    - Probleem Database connectie mislukt
    - Oplossing Controleer of MariaDB container draait en netwerk verbinding
- **Wrong password**
    - Probleem: GET /api/users geeft deze melding
    - Oplossing: Voeg `?pwd=mypassword` toe aan de URL

### Best practices voor API testing

1. **Gebruik environments**: Maak variabelen voor `baseUrl` zodat je makkelijk kan switchen tussen localhost en productie
2. **Test volgorde**: Test eerst GET endpoints voordat je CREATE/UPDATE/DELETE test. Zo kan je de GET endpoints gebruiken om na te gaan of je CREATE/UPDATE/DELETE geslaagd is.
3. **Cleanup**: Gebruik DELETE requests om test data op te ruimen na testen
4. **Automated tests**: Gebruik eventueel de test functionaliteit van Thunder Client voor automatische validatie.

Met deze setup kun je nu efficiënt je Flask REST API testen vanuit VS Code, zonder externe tools nodig te hebben zoals `curl` of **Postman** of ...! 

