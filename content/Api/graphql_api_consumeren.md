---
title: "GraphQL api consumeren in laravel"
weight: 9
author: Arne Duyver
draft: false
---

## Wat is GraphQL?

GraphQL is een querytaal voor APIs en een runtime voor het uitvoeren van die queries. GraphQL biedt een efficiënter, krachtiger en flexibeler alternatief voor REST APIs. Met GraphQL kunnen clients precies de gegevens opvragen die ze nodig hebben, waardoor het overvragen of ondervragen van data wordt voorkomen.

## Werking

GraphQL werkt via één enkel endpoint (meestal `/graphql`) en gebruikt een schema om de beschikbare datatypes en hun onderlinge relaties te definiëren. Het schema bestaat uit:

- **Types**: Definities van objecten, velden en hun relaties.
- **Queries**: Verzoeken om data op te halen.
- **Mutations**: Verzoeken om data aan te passen.
- **Subscriptions**: Realtime data-updates.

Een GraphQL-query lijkt op de vorm van de JSON-response die het zal returnen. Clients specificeren precies welke velden ze nodig hebben, en de server haalt alleen die velden op, wat zorgt voor efficiëntie.

### Belangrijke Componenten

1. **Schema**: Definieert de beschikbare datatypes en queries in de API.
2. **Resolvers**: Functies die aangeven hoe de data voor elk veld in het schema opgehaald moet worden.
3. **Uitvoering van Query's**: De client stuurt een query, en de GraphQL-runtime vergelijkt de query met het schema en voert deze uit via de resolvers.

Voorbeeld van een GraphQL-query:
```graphql
query {
  user(id: "1") {
    name
    email
    posts {
      title
    }
  }
}
```

De bovenstaande query vraagt de `name` en `email` van een gebruiker, samen met de bijbehorende `posts`, maar alleen de `title` van elke post.

## Voordelen van GraphQL

- **Efficiënt Data ophalen**: Clients vragen alleen de specifieke data op die ze nodig hebben, wat under- en overfetching minimaliseert en zo de netwerkbelasting vermindert.
- **Enkel endpoint**: In tegenstelling tot REST, dat vaak meerdere eindpunten vereist, gebruikt GraphQL één endpoint voor alle verzoeken, wat het API-beheer vereenvoudigt.
- **Sterk Getypeerd Schema**: Een goed gedefinieerd schema functioneert als een contract tussen client en server, wat het eenvoudiger maakt om data te begrijpen en te valideren.
- **Geen Versies Nodig**: Met GraphQL kunnen nieuwe velden aan het schema worden toegevoegd zonder bestaande clients te beïnvloeden, waardoor de noodzaak voor versiebeheer afneemt.
- **Realtime Data met Subscriptions**: GraphQL ondersteunt realtime-updates via subscriptions, ideaal voor applicaties die live data-updates nodig hebben.
- **Ontwikkelaarservaring**: Tools zoals GraphiQL of GraphQL Playground laten ontwikkelaars queries verkennen en testen, wat productiviteit en foutoplossing verbetert.

GraphQL is populair geworden vanwege zijn flexibiliteit en de mogelijkheid om dataverzoeken te stroomlijnen, waardoor het een ideale keuze is voor applicaties met complexe databehoeften.

## GraphQL api oproepen in Laravel vanuit de backend met PHP.
Om een ​​Laravel-project te maken dat communiceert met de GraphQL API met behulp van JavaScript in de frontend, moeten we volgende zaken doen:

1. Laravel project aanmaken
2. Gebruik JavaScript om de Flask API aan te roepen
3. Creëer de nodige Laravel controllers
4. Configureer routes

### Laravel project aanmaken
```bash
composer create-project laravel/laravel consumeApi
# change database type to 'mysql' in .env
cd /consumeApi
php artisan migrate
# start onze website, host 0.0.0.0 is nodig om correct met docker te werken
php artisan serve --host 0.0.0.0
```

### Demo 4: GraphQl api consumeren in Laravel
We een endpoint voorzien in de `routes/web.php` en we gaan de [`PHP GraphQL Client`](https://github.com/softonic/graphql-client) module gebruiken om de GraphQL queries uit te voeren. (`composer require softonic/graphql-client`):

<details open>
    <summary><i><b>Klik hier om de code te zien/verbergen voor `routes/web.php`</b></i></summary>
    <p>


```php
<?php

use Illuminate\Support\Facades\Route;
use Illuminate\Http\Request;
use Softonic\GraphQL\ClientBuilder;

Route::get('/', function () {
    return view('welcome');
});

Route::get('/graphQl_1', function () {
    $client = ClientBuilder::build('https://countries.trevorblades.com/graphql');
    $query = 'query ExampleQuery {
        countries {
            name
        }
    }';
    $variables = [];
    $response = $client->query($query,$variables);
    dd($response);
});

Route::get('/graphQl_2', function () {
    # Maak verbinding met de GraphQL API
    $client = ClientBuilder::build('https://countries.trevorblades.com/graphql');
    
    # GraphQL voordeel: je vraagt alleen de data op die je nodig hebt - geen over- of onder-fetching
    # Deze query demonstreert het ophalen van meerdere verschillende datatypes in één request
    $query = 'query ExampleQuery {
            # Haal een specifiek continent op (Europa) met alle landen
            continent(code: "EU") {
                name        # Continent naam
                countries {
                    name        # Landnaam
                    capital     # Hoofdstad
                    currency    # Valuta
                }
            }
            # Haal alle continenten op met basis informatie
            continents {
                name        # Continent naam
                code        # Continent code (bijv. EU, AS, NA)
            }   
        }';

    # Geen variabelen nodig voor deze query
    $variables = [];

    # Voer de query uit en dump het resultaat
    $response = $client->query($query,$variables);
    dd($response);
});

Route::get('/graphQl_3', function () {
    # Maak verbinding met de GraphQL API
    $client = ClientBuilder::build('https://countries.trevorblades.com/graphql');

    # Deze query demonstreert het gebruik van variabelen in GraphQL
    # Variabelen maken queries herbruikbaar en veiliger tegen injectie-aanvallen
    $query = 'query Query($countryCode: ID!) {
                # Het uitroepteken (!) betekent dat deze variabele verplicht is
                country(code: $countryCode) {
                    name            # Volledige landnaam
                    capital         # Hoofdstad
                    currency        # Valuta codes (kan meerdere zijn)
                    phone           # Internationale telefooncode
                    continent {     # Geneste object: continent informatie
                        name        # Naam van het continent
                    }
                    languages {     # Array van objecten: alle gesproken talen
                        name        # Naam van elke taal
                    }
                }
            }';
    
    # Definieer de variabelen in JSON formaat
    # Deze waarden worden veilig ingevoegd in de query waar $countryCode staat
    $variables = ["countryCode"=> "BE"]; # BE = België

    # Voer de query uit met de meegegeven variabelen
    $response = $client->query($query,$variables);
    
    # Pak specifieke data uit de response uit
    # getData() geeft de 'data' sectie terug, ["country"] pakt het land object eruit
    dd($response->getData()["country"]);
});
```
</p>
</details>

