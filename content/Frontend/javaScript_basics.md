---
title: "JavaScript"
weight: 3
author: Arne Duyver
draft: false
---

_bron 1: Responsive Web Design with HTML5 and CSS - 4th edition - Ben Frain_</br>
_bron 2: [W3Schools](https://www.w3schools.com/html/default.asp)_


## JavaScript
JavaScript wordt gebruikt om interactie en dynamische functionaliteit aan je website toe te voegen. Het stelt ontwikkelaars in staat om webpagina's te manipuleren, gebruikersacties te verwerken en te communiceren met servers zonder de pagina opnieuw te laden. JavaScript is veelzijdig en wordt ondersteund door alle belangrijke webbrowsers. Het maakt deel uit van de frontend is vaak niet of minimaal aanwezig op statische websites.

Wat nog belangrijk is om te weten, is dat de JavaScript standaard (**ECMAScript**) beheerd wordt door _Ecma International_, vroeger ook wel de _European Computer Manufacturers Association (ECMA)_ genoemd. Een overzicht van de historische details zijn terug te vinden op de JS [wikipage](https://en.wikipedia.org/wiki/JavaScript).

{{% notice info %}}
We gaan er vanuit dat je al een basiskennis programmeren bezit. In deze sectie zoomen we vooral in op de interactie die JavaScript ons biedt met HTML-elementen en andere functionaliteiten die specifiek zijn voor het maken van websites.
{{% /notice %}}

### Hoe JavaScript toevoegen aan je website
Je kan JavaScipt rechtstreeks in je html-document toevoegen via het `<script>` element. Deze manier wordt echter niet aangeraden. Je kan het `<script>-element` toevoegen in de sectie `head` of in de `body` van je HTML-document. Het is echter aan te raden om scripts onderaan het `<body>-element` toe te voegen, omdat dit de laadtijd van een webpagina kan verbeteren. [\[bron\]](https://www.shecodes.io/athena/37573-where-does-the-script-tag-go-in-html#:~:text=You%20can%20add%20the%20%3Cscript,body%3E%20of%20your%20HTML%20document.)

```html
...
<script type="text/javascript">
  //Jouw code komt hier
</script>
...
```
JavaScipts worden echter beter verzameld in een `scripts`-directory. Op die manier heb jee betere scheiding van verantwoordelijkheden tussen html en JavaScript (JS). Je kan ook zeer makkelijk verschillende JS-scripts aanmaken voor je verschillende webpaginas. Je kan hier weer het `<script>-element` voor gebruiken, maar dit keer laten we de content leeg en verwijzen we met behulp van het `scr`-attribuut naar de juiste JS-file.
```html
...
<script type="text/javascript" src="./path_to_your/javascript_file.js"></script>
...
```
Een veelgebruikte directory structuur voor het beheren van al je client-side files ziet er zo uit.
```
root/
|
+--- index.html
|   
+--- scripts/
|   |
|   +--- main.js
|   ...
+--- styles/
    |
    +--- main.css
    ...
...
```

### Developper tools and logging
Om onze JS code te debuggen gaan we veel gebruik maken van de developper tools die beschikbaar zijn in je browser. In google chrome kan je de develepper tools openen met `F12`. De plaats waar we de meeste tijd gaan doorbrengen in de `console`. We kunnen namelijk allerhande informatie laten printen naar de console. Dit kan op volgende manieren:
```javascript
console.log("Ik ben een normaal log bericht");
console.debug("Ik ben een debug bericht");
console.error("Ik ben een error");
console.info("Ik ben info");
console.warn("Ik ben een warning");
```
In de developper console kan je dan ook filteren op de verschillende soorten logberichten.

### Webcontent inspecteren
Om informatie over bepaalde content op te vragen moeten we eerst weten hoe we HTML-elementen kunnen opvragen om ze daarna te inspecteren. 

Eén van de gemakkelijkste manieren om een specifiek element op te vragen is op basis van de `id`:
```javascript
document.getElementById("idName"); //returns an HTMLObject
``` 
**Als je een element in een HTML-pagina wilt opvragen, begin je altijd met het refereren naar het documentobject.**
Andere manieren om een element te krijgen is via de klassenaam of de tagnaam. Hier wordt dan echter steeds een lijst greturned op basis van de volgorde in het HTML-bestand.
```javascript
document.getElementsByClassName("className");        //returns an HTMLCollection
document.getElementsByName("valueOfAttributeName");  //returns an HTMLCollection
document.getElementsByTagname("tagName");            //returns an HTMLCollection
```

Verder kan je ook `CSS-selector syntax` gebruiken om een element op te vragen. De `querySelector`-functie geeft telkens het eerste `HTMLObject` terug dat voldoet aan de query. De `querySelectorAll`-functie geeft een `NodeList` met alle objecten terug die voldoen aan de query.
```javascript
document.querySelector("#idName");     //returns an HTMLObject
document.querySelector(".className");  //returns an HTMLObject
document.querySelector("tagName.className:not(.className) tagName[attibuteName='attibuteValue']"); //returns an HTMLObject

document.querySelectorAll("#idName");  //returns a NodeList
```
_Sommige functies geven een HTMLCollection terug terwijl anderen een NodeList teruggeven. Een NodeList bevat bovenop descendent HTML-elementen ook de stukken tekst die tussen andere eventuele HTML-elementen gebruikt worden. Dit wordt belangrijk bij het opvragen van `children` (HTMLCollection) of `childNodes` (NodeList)_

#### Informatie over elementen inspecteren
Nu we een element kunnen opvragen, kunnen we meer specifieke informatie over dat element inspecteren zoals de waarde van attributen, textcontent, descendents ...
```javaScript
console.log(html_object.innerText);
console.log(html_object.textContent);  
console.log(html_object.innerHTML); 
console.log(html_object.children); 
console.log(html_object.childNodes); 

console.log(html_object.getAttributeNames());
console.log(html_object.getAttribute("attributeName"));
console.log(html_object.getAttributeNode("attributeName"));

console.log(html_object.style.fontsize); //Dit werkt niet altijd op deze manier wanneer er specifieke CSS styling werd toegepast. 
//Gerbuik dan volgende methode in de plaats:
console.log(window.getComputedStyle(html_object, null).getPropertyValue('font-size'));
```

{{% notice info %}}
Je kan de console gebruiken om te inspecteren welke attributen een HTML-element allemaal bevat. Bovendien kan je in de documentatie steeds terugvinden wat de return-typen zijn van de verschillende functie. Je kan echter ook zeer snel het type controleren met de JS-functie `typeof naamObject`.
{{% /notice %}}

{{% notice warning %}}
Wanneer een attribuut of object niet gevonden wordt, wordt meestal `undefined` gereturned.
{{% /notice %}}

#### Webcontent manipuleren
Je kan nu ook snel content aanpassen door de attribuut waarde te veranderen:
```javaScript
html_object.innerText = "nieweTekst";
html_object.innerText = '<h1 id="nieuweId" > Nieuwe header </h1>'; // Je kan dus zelfs dynamisch HTML-elementen toevoegen of verwijderen 
html_object.style.fontsize = "15px";

html_object.setAttribute("attributeName", "newValue");

//Examples
voornaamInput_object.setAttribute("value", "Arne");
inputCheckbox_object.setAttribute("checked", "true");
```

### Gebruiker interactie verwerken
Je kan rechtstreeks in een HTML-element functies (of zelfs gewoon java code) toevoegen om uitgevoerd te worden bij bepaalde acties van de gebruiker.
```html
<button onclick="functionName">Click me</button>
<button onclick="console.log('button clicked')">Click me</button>
```
Dit doe je echter beter in de JavaScript file zelf door `eventListeners` te koppelen aan de gewenste HTML-elementen. Een aantal voorbeelden vind je hieronder. Je kan onder andere een listener toevoegen voor `click`, `mouseover`, `mouseout`, `mousemove` ...
```javascript
html_object.addEventListener("click", functionName);
// Je kan ook rechtrstreeks functies definiëren als parameter:
html_object.addEventListener("click", function(){alert("I was clicked");});
// Of door gebruik te maken van de pijl notatie:
html_object.addEventListener("click", () => {alert("I was clicked");});

//Je kan ook eventListeners toevoegen aan je window
window.addEventListener("resize", function() { console.log("resized window");});
```

#### Met alert, confirm en prompt kan je snel pop-up berichtjes toevoegen
```javascript
//alert
alert("Alert message, click 'ok' to continue.");
//confirm
var confirmOutput = confirm("Confirm message, click 'ok' or 'cancel' to continue.");
console.log(confirmOutput)
//prompt
var promptOutput = prompt("The prompt message");
console.log(promptOutput)
```

### Basis JavaScript syntax
#### Variabelen
```javascript
var x = 1;                    // met 'var' definiëer je een variabele die beschikbaar blijft binnen de functie scope.
let y = "let";                // met 'let' definiëer je een variabele die beschikbaar blijft binnen de block scope. (bv enkel binnenin die if-statement)
const z = "Ik verander niet"; // met 'const' definiëer je een variabele die niet meer verandert
```
#### Types
```javascript
// Numbers:
let length = 16;
let weight = 7.5;

// Strings:
let color = "Yellow";
let lastName = "Johnson";

// Booleans
let x = true;
let y = false;

// Object:
const person = {firstName:"John", lastName:"Doe"};
const person = {
  firstName: "John",
  lastName : "Doe",
  id       : 5566,
  fullName : function() {
    return this.firstName + " " + this.lastName;
  }
};

// Array object:
const cars = ["Saab", "Volvo", "BMW"];

// Date object:
const date = new Date("2022-03-25");
```

#### If, switch, for, while en try-catch
```javascript
//IF-STATEMENT
if (condition1) {
  //  block of code to be executed if condition1 is true
} else if (condition2) {
  //  block of code to be executed if the condition1 is false and condition2 is true
} else {
  //  block of code to be executed if the condition1 is false and condition2 is false
}

//SWITCH
switch(expression) {
  case x:
    // code block
    break;
  case y:
    // code block
    break;
  default:
    // code block
}

//FOR-LOOP
for (let i = 0; i < cars.length; i++) {
  text += cars[i] + "<br>";
}

//WHILE
while (condition) {
  // code block to be executed
}

//TRY CATCH
try {
  //Block of code to try
}
catch(err) {
  //Block of code to handle errors
}
finally {
  //Block of code to be executed regardless of the try / catch result
}
```
#### Functies en klassen definiëren
```javascript
//FUNCTIE
function(parameter) {
  //mijn code
  return result;
}

//EXAMPLE Class
class Car {
  constructor(name, year) {
    this.name = name;
    this.year = year;
  }
  age(x) {
    return x - this.year;
  }
}
const myCar = new Car("Ford", 2014);
```

### Content wegschrijven naar localstorage of sessionstorage
Je kan met JavaScript ook data opslaan in je browser. Dit is steeds een `key-value` pair waarbij beide waarde `Strings` zijn. Wegschrijven naar `localStorage` behoudt de data tussen sessies. Wegschrijven naar `sessionStorage` behoudt de data tussen sessies **NIET**. Maak gebruik van `JSON` om gemakkelijk data objecten op te slaan:
```javascript
//Het op te slagen object
var testObject = { 'one': 1, 'two': 2, 'three': 3 };
//Opslaan in localStorage
localStorage.setItem('testObject', JSON.stringify(testObject));
//Inladen vanuit localStorage
var retrievedObject = localStorage.getItem('testObject');
var retrievedTestObject = JSON.parse(retrievedObject);

//idem voor sessionStorage
```

### Async functions, promises, await & fetch from api's
Soms wil je dingen opvragen of kan het een tijd duren voordat een functie een return geeft. Om te voorkomen dat je gedurende die tijd niet kan interageren met de website, moet je gebruik maken `async` functions. Ergens in je functie gebruik je dan het woord `await` zodat de rest van je funtie verder gaat wanneer je een return waarde hebt ontvangen. Tijdens het wachten kan dan andere code uitgevoerd worden.

```javascript
function resolveAfter2Seconds() {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve('resolved');
    }, 2000);
  });
}

async function asyncCall() {
  console.log('calling');
  const result = await resolveAfter2Seconds();
  console.log(result);
  // Expected output: "resolved"
}

asyncCall();
```

Een voorbeeld waarvoor je vaak de `async function` gebruikt is bij het gebruik van api's om data op te halen/weg te schrijven:
```javascript
async function cuteDogPicture() {
  fetch('https://dog.ceo/api/breeds/image/random')
    .then((response) => {           // Parameter 'response' refers to the return of the function above this (fetch)
      return response.json();
    })
    .then((myContent) => {          // Parameter 'myContent' refers to the return of the function above this
      document.innerHTML = "<img src='" + myContent['message'] + "'/>";
    });
}

window.addEventListener("click", () => { cuteDogPicture() });
```

## Canvas met [p5.js](https://editor.p5js.org/)


## Opdracht
Voeg op minstens 2 plaatsen JavaScript toe aan je portfolio website. Ten eerste zorg je ervoor dat wanneer mensen op de 'submit'-knop drukken in je contactenformulier dat je de data van de inputvelden op een correcte manier in een object variabele opslaat. Sla daarna de data op in je `sessionStorage` door gebruik te maken van JSON. Zorg er ook voor dat wanneer je je contactformulier opent het formulier al is ingevuld met de laatst opgeslagen gegevens indien ze bestaan.

Voeg verder op home-pagina nog een extra sectie toe genaamd 'Interactie met p5.js'. Gebruik de modules van de p5.js library om een canvas te tekenen en een leuke creatieve interactie te creëren met de bezoekers van je website. Doe dit bijvoorbeeld door een kleine animatie te starten die verandert op basis van de muis positie. 

Volgende les komen enkele studenten hun interactie met de portfolio webpagina kort voorstellen.