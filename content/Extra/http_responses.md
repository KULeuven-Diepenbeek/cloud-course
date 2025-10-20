---
title: "HTTP response codes"
weight: 3
author: Arne Duyver
draft: false
---

## Veelvoorkomende HTTP-responscodes in REST API's

In REST API's worden HTTP-responscodes gebruikt om aan te geven of een verzoek succesvol is verwerkt of niet. Hieronder staan de meest voorkomende codes met hun betekenis:

* **`200 OK`**: Het verzoek is succesvol uitgevoerd en de gevraagde data wordt teruggegeven.
* **`201 Created`**: Een nieuwe resource is aangemaakt, bijvoorbeeld na een succesvolle `POST`-aanvraag.
* **`204 No Content`**: De operatie is geslaagd, maar er wordt geen data teruggestuurd (vaak gebruikt bij `DELETE`).
* **`400 Bad Request`**: Het verzoek is ongeldig, bijvoorbeeld door ontbrekende velden of onjuiste data.
* **`401 Unauthorized`**: Authenticatie is vereist of mislukt; de client moet geldige inloggegevens verstrekken.
* **`403 Forbidden`**: De client is geauthenticeerd maar heeft geen toestemming om de gevraagde resource te benaderen.
* **`404 Not Found`**: De gevraagde resource bestaat niet of is niet beschikbaar.
* **`500 Internal Server Error`**: Er is een onverwachte fout opgetreden aan de serverzijde waardoor het verzoek niet kon worden verwerkt.
