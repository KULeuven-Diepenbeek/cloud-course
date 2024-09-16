---
title: "PHP: Hypertext Preprocessing"
weight: 1
author: Arne Duyver
draft: false
---

_bron 1: Responsive Web Design with HTML5 and CSS - 4th edition - Ben Frain_</br>
_bron 2: [W3Schools](https://www.w3schools.com/php/default.asp)_


## PHP: Hypertext Preprocessing

We gaan met PHP leren:
- dynamische webpagina's maken
- onze website te beveiligen
- te communiceren met een database

### De PHP syntax
PHP is een scripting language die voornamelijk ontworpen werd en gebruikt wordt voor webdevelopment. De PHP code kan onmiddellijk in HTML geïntegreerd worden en wordt uitgevoerd aan de serverside. Aangezien de code aan de serverside uitgevoerd wordt, blijft de broncode verborgen voor de client (goed voor de veiligheid).

PHP is al een oudere taal maar wordt nog steeds gebruikt in ongeveer 78% van alle websites (Python 2%). Er worden ook nog steeds updates geproduceerd. PHP is namelijk specefiek ontwikkeld voor webdevelopment en Python is een meer all-round language, wat dit verschil meer verklaard. Dit wil niet zeggen dat je enkel webapplicaties kan maken met PHP, Je kan ook standalone applicaties maken maar hiervoor wordt PHP minder gebruikt. Enkele prominenten bedrijven die nog PHP gebruiken als deel van hun backend zijn: Facebook, Wikipedia, Canva ...

Om PHP te leren gaan we gebruik maken van [XAMPP](https://www.apachefriends.org/). Met behulp van XAMPP kunnen we op onze pc een lokale server laten draaien die onze backend implementeerd en onze website gaat kunnen displayen. Later gaan we PHP zien gebruikt worden in het populaire framework Laravel. Laravel gaat voor ons al veel organisatorisch werk verrichten.

Een PHP file kan ook enkel HTML code bevatten. Je kan dus gewoon bij alle documenten met de `.html` extentie de extentie vervangen door `.php`. Om dan effectief PHP code te laten uitvoeren in gebruik je volgende syntax: 
```php
<html>
<head>...</head>
<body>
  <?php
    //Je php-code komt hier
  ?>
</body>
</html>
```

Je kan ook pure PHP-code gebruiken in je `.php` file (Bijvoorbeeld pagina dient enkel voor dataprocessing maar bevat geen visuele elementen om te tonen). Je opent dan ook nog met `<?php` maar de sluitingstag wordt dan weggelaten:

```php
<?php
//Je php-code komt hier
```

### XAMPP
Om onze site te hosten moeten al onze bronbestanden staan in de `htdocs`. Dan kan je surfen naar [http://localhost/](http://localhost/) om je webpagina te bezoeken.

## Opdrachten
1. Overloop de PHP [Tutorials](https://www.w3schools.com/php/default.asp) op W3Schools. 
2. Maak op W3Schools.com de [exercises](https://www.w3schools.com/php/php_exercises.asp) rond php (Je mag advanced php overslaan).
3. Maak op W3Schools de PHP [Quiz](https://www.w3schools.com/php/php_quiz.asp). (Sommige antwoorden hebben we nog niet gezien, maar 90% wel)
4. Gebruik een HTML form, POST-method en PHP om een rekenmachine webpagina te maken.

## [Connect to database + security demos](https://github.com/KULeuven-Diepenbeek/fsweb-demos-exercises-student)

## Opdrachten
1. Maak een volledig werkende signup, signin website die gebruik maakt van session-storage om bij te houden welke gebruiker is ingelogd. Zorg voor login, logout, change pwd. Zorg ervoor dat wachtwoorden veilig opgslagen worden in de database. De index.php moet je automatisch redirecten naar de login/signup page als je er nog geen gebruiker ingelogd is.