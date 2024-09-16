---
title: "Security (enkele voorbeelden)"
weight: 1
author: Arne Duyver
draft: false
---

Hieronder worden een aantal bekend cyberaanvallen besproken, hoe ze werken en hoe je je website er kan tegen beschermen.

## HTML injection attacks
**Probleem: Je HTML-code bevat een inputveld en die input gebruik je terug om als HTML code gepersonaliseerde content te tonen.**

_Situatie_: De gebruiker begint HTML-code te schrijven in de input velden. Bijvoorbeeld `<script>database.connect; console.log(db.alleWachtwoorden)</script>` <br/>
_Thread level_: ZEER HOOG <br/>
_Oplossing_: Wanneer je input van de gebruiker terug wil gebruiken als HTML-code, strip de input dan eerst van HTML-tags. (In PHP bestaat hier een voorgemaakte methode voor, maar je kan dit ook in JavaScript doen bijvoorbeeld)

## Wachtwoorden veilig opslaan in een database
**Probleem: Je database wordt gehacked.**

_Situatie 1_: Je wachtwoorden staan simpelweg in plain-text opgeslagen in de database. <br/>
_Thread level_: ZEER HOOG <br/>
Hackers kunnen simpelweg alle wachtwoorden uitlezen en verkopen aan de hoogste bieder op het dark web. <br/>
_Oplossing_: Hash wachtwoorden voordat je ze opslaat in de database. Zo wordt elk wachtwoord veranderd in een random string van karakters. Van de random string karakters is het zeer moeilijk om terug naar het originele wachtwoord te vinden. <br/>
(Hoe weet je dan of een gebruiker het juiste wachtwoord opgeeft? Elke zelfde waarde die je hashed zal ook dezelfde hashwaarde krijgen. Je kan nu dus simpelweg de hashwaarde van de gebruiker vergelijken met de hashwaarde in de database om te controleren of het het juiste wachtwoord was)

_Situatie 2_: Je wachtwoorden staan gehashed opgeslagen in de database. <br/>
_Thread level_: HOOG <br/>
Er bestaan al gelekte lijsten van gehashte wachtwoorden gekoppeld aan gebruikers. Met veel tijd en geduld kan je uiteindelijk het wachtwoord achterhalen die tot de juiste hash leidt en opslaan in simpele lookup tabellen (die al bestaan). Wanneer hackers dus een gehashte wachtwoorden vinden kunnen ze die simpel vergelijken met die tabbellen en toch wachtwoord te pakken krijgen<br/>
_Oplossing_: Voeg voor elk wachtwoord een andere random waarde toe voordat je hashed zodat hackers niet meer de juiste hash kunnen terugvinden in de tabellen<br/> 
(Je moet nu wel per wachtwoord deze random waarde = de _salt_ ook opslaan in de database)

_Situatie 3_: Je wachtwoorden met salt staan gehashed opgeslagen in de database. <br/>
_Thread level_: MIDDEL <br/>
Als een hacker je database hackt heeft hij zowel toegang to de hashes als de corresponderende salt-waarden. Met genoeg tijd en moeite zal hij dus nog steeds het wachtwoord kunnen vinden.<br/>
_Oplossing_: Voeg voor elk wachtwoord met salt ook nog een vast _secret_ (= de _pepper_) toe voordat je hashed. Sla die waarde nu veilig op ergens weg van de database, bijvoorbeeld op je backend server. Op deze manier moeten zowel je database als je server gehackt worden voordat ze de wachtwoorden kunnen bemachtigen.

## Session Hijacking
**Probleem: Je verbind met je bank met een http-connectie en je identiteit tussen pagina's blijft behouden dankzij session tokens.**
_Situatie_: Omdat je een http-connectie gebruikt is de communicatie tussen jou en de server niet geencrypteerd, een persoon op dezelfde wifi kan dus in plain-text de communicatie uitlezen. Je session token wordt dus ook in plain-text meegegeven. De andere persoon vind je token en gebruikt hem in zijn browser om jou te impersoneren en doet een overschrijving naar zijn eigen bankrekening van 1000 euro.<br/>
_Thread level_: ZEER HOOG <br/>
_Oplossing_: Gebruik steeds een http**s**-connectie om te surfen op het web zodat alle communicatie geencrypteerd is, dit maakt het alleszins moeilijker om session tokens te stelen. **(Tegenwoordig is er geen enkele reden om nog naar een website te surfen over enkel een http-connectie)**


## Cross-Site Request Forgery
_Situatie_: Een cyberaanvaller op een andere lokatie heeft toevallig jouw sessiegegevens kunnen bemachtigen en probeert met jou session token request te sturen naar de server vanuit een andere applicatie. De CORS (= Cross-Origin Resource Sharing) is enabled bij je website<br/>
_Thread level_: HOOG <br/>
_Solution_: Cross-Origin Resource Sharing is enabled op je website waardoor je server ook requests van andere domeinen kan ontvangen in plaats van enkel requests die door zichzelf gemaakt zijn. Door CORS enkel te enablen waar nodig en zeker zoveel mogelijk te disablen, kan je je tegen CSRF beveiligen. (Tegenwoordig is in de meeste browseres CORS automatisch gedisabled)

