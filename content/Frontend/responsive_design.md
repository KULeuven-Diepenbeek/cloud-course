---
title: "Responsive Design"
weight: 5
author: Arne Duyver
draft: false
---

## Let's talk about Units
Zoals al eerder vermeld bestaan er verschillende soorten units in HTML/CSS, maar waarvoor gebruik je welke unit nu het beste? Hieronder vind je een aantal tips. 

Maar eerst volgt er nog een kleine uitleg over de abstracte unit **pixel** of `px`. Er bestaat namelijk wel wat verwarring op het internet over wat hoe groot `1px` juist is. Allereerst is het de kleinste unit die je kan gebruiken, maar hoe groot is het juist. We gaan geen exacte meting meegeven maar vertellen je wel waarvoor de pixel dient. De pixelgrootte is verschillend voor verschillende schremgrootten en met goede reden. De grootte van de pixel heeft te maken met de aangeraden kijkafstand ten opzichte van het scherm. De pixel unit zorgt er namelijk voor dat `10px` op een klein scherm er even groot zou moeten uitzien als `10px` op een groot scherm wanneer je rekening houd met de kijkafstand tot de twee schermen. In werkelijkheid is `10px` op het grote scherm groter, maar als je op de correcte afstand van het scherm zit zouden de groottes overeen moeten komen. Dit maakt de **pixel** de ideale basis unit voor je design. 

<figure>
<img src="/img/pixel_unit.png" alt="drawing" style="min-width: auto;"/>
<figcaption>Schema werking pixel unit <a href="https://fantasai.inkedblade.net/style/specs/css2.1/px-unit">bron</a></figcation>
</figure>

### Browser default font-size
De browser default font-size is `16px` voor de meeste moderne browsers. Gebruikers kunnen dit echter aanpassen naar hun accessibility noden. Daarom is het belangrijk specifieke absolute font-sizing te vermijden. Op die manier blijven de accessibility features bruikbaar.

Gebruik daarom steeds de `rem` unit om font-size mee te geven zodat alles mee scaled met de default waarde gebruikt door browser. We gebruiken `rem` omdat die steeds relatief is t.o.v. de root font-size. `em` is scaling t.o.v. de eigen parent maar dit kan snel de leesbaarheid van je code vermideren. De `em` unit is wel handig om bijvoorbeeld breedte, hoogte, padding, margins ... van een element in te stellen omdat je op die manier je grootte baseerd op font-size van zijn content. 

### % vh vw
- **Percentage** units zijn altijd relative t.o.v. hun parent. 
- **Viewport** units zijn altijd relative t.o.v. de schermgrootte.

Met `vh` kan je er bijvoorbeeld voor zorgen dat content steeds 80% van de hoogte van je scherm inneemt (`min-height: 80vh;`). 
**Let op** wanneer je een `vw` van 100 gebruikt, aangezien de viewport geen rekening houd met scrollbars, zal je bij 100 altijd een zeer kleine "irritante" horizontale scrollbar krijgen.

### ch
Een laatste unit die je voor een specifiek geval kan gebruiken is de `ch` (of character width) unit. Een bepaalde design filosofie stelt dat het een good practice is om textcontainers niet breder te maken dan 60 karakters. Door de styling `max-width: 60ch;` toe te passen, wordt automatisch deze good practice toegepast.


**99% van de tijd zal je dus enkel met de hierboven vermelde units werken**

## Starting fresh

Start je CSS file met volgende code om enkel "ongewenst" default gedrag van de browser te overschrijven:

```CSS
*,
*::before,
*::after {
  box-sizing: border-box; /* Zorgt ervoor dat default padding en border worden meegerekend met breedte en hoogte */
}

* {
  margin: 0;  /* Zorgt ervoor dat elk element default geen margin heeft */
  padding: 0; /* Zorgt ervoor dat elk element default geen padding heeft */
}
```

## Media Queries

<figure>
<img src="/img/mediaQuery_syntax.jpg" alt="drawing" style="height:auto;"/>
<figcaption>Media query syntax <a href="https://dev.to/codewithtee/css-media-queries-16mj">bron</a></figcation>
</figure>

- **Media-type**: kies tussen `screen`, `print`, `speech` of `all` (default)
- **Expression to test**: verschil `min-width`, `max-width`. Wanneer je je website voor desktop designed dan gebruik je media queries met `max-width` om ze ook responsive te maken op kleinere viewports. Vice versa als je je website designed voor mobile, gebruik dan `min-width` op responsive te zijn op grotere viewports.
- **Conditional CSS**: style je elementen zoals ze er moeten uitzien op de gequeriede media.

_Je kan dus voor veel verschillende screen sizes custom CSS schrijven._

Voorbeeld:
```CSS
@media (min-width: 500px){
  html {
    color: red;
  }
}
```

### Extra media queries

Je kan ook verschillende CSS files gebruiken om de verschillende layouts te definiëren. Via het `<link>` element kan je dan alle CSS bestanden laten laden bij de verschillende omstandigheden:

```html
<!-- Load in `portrait-screen.css` when screen is in portrait mode -->
<link rel="stylesheet" media="screen and (orientation: portrait)" href="portrait-screen.css"/>
```

Je kan hetzelfde breiken met imports in je `main.css` file:

```css
@import url("portrait-screen.css") screen and (orientation: portrait);
```

Je kan media queries ook **inverteren** met behulp van het keyword `not`:
```CSS
@media not (orientation: portrait) { ... }
```
Je kan media queries ook **combineren** met behulp van het keyword `and`:
```CSS
@media screen and (orientation: portrait) and (min-width: 500px) { ... }
```

**Lijst met veelgebruikte eigenschappen waar media queries op kunnen testen**:
- `width`: The viewport width.
- `height`: The viewport height.
- `orientation`: This capability checks whether a device is portrait or landscape in orientation.
- `aspect-ratio`: The ratio of width to height based on the viewport width and height. A
16:9 widescreen display can be written as aspect-ratio: 16/9.
- `color`: The number of bits per color component. For example, min-color: 16 will check
that the device has 16-bit color.
- `color-index`: The number of entries in the color lookup table (the table is how a device
changes one set of colors to another) of the device. Values must be numbers and cannot
be negative.
- `monochrome`: This capability tests how many bits per pixel are in a monochrome frame
buffer. The value would be a number (integer), for example, monochrome: 2, and cannot
be negative.
- `resolution`: This capability can be used to test screen or print resolution; for example,
min-resolution: 300dpi. It can also accept measurements in dots per centimeter; for
example, min-resolution: 118dpcm.
74 Media Queries and Container Queries
- `scan`: This can be either progressive or interlace, features largely particular to TVs. For
example, a 720p HD TV (the “p” part of 720p indicates “progressive”) could be targeted
with scan: progressive, while a 1080i HD TV (the “i” part of 1080i indicates "interlaced")
could be targeted with scan: interlace.
- `grid`: This capability indicates whether or not the device is grid orbitmap-based.
- `prefers-color-scheme`: The theme selected by the user in the browser settings ("light" or dark).

_Alle bovenstaande functies, met uitzondering van scan, raster en prefers-color-scheme, kunnen voorafgegaan worden door `min-` of `max-` om
bereiken te maken._

## Workflow

**Workflow versie 1**: ontwerp je website voor een specifieke viewport, maar laat zo veel mogelijk aan de defaults van de browser over. Test daarna voor andere viewports en pas eventueel je CSS code aan zodat ze automatisch meer responsive is. Schrijf ten slotte scpecifieke media queries voor die dingen die niet automatisch aangepast kunnen worden.

**Workflow versie 2**: Bepaal op voorhand een aantal media query breakpoints (XL, L, M, S, XS) en maak elk ervan responsive.

## Positioning (herhaling)

- `position: absolute;` item is verwijderd van het document.
  - `top`, `right`, `bottom`, `left` unlocked.
  - zet `position: relative;` in parent om het element relative te positioneren t.o.v. van parent i.p.v. hele document.
  - gebruik `z-index` om elementen naar voor te halen of naar achter te brengen.
  - (Wanneer je elementen met negatieve waarden niet meer passen op het scherm< verschijnt een scroll bar.Verwijder de scroll bar met `overflow: hidden;` in parent or body)
- `position: relative;` position relative t.o.v. parent en blijven in de normal flow van het document.
  - `top`, `right`, `bottom`, `left` unlocked.
- `position: static;` default, er gebeurt niks.
- `position: fixed;` gelijkaardig aan absolute en dus ook verwijderd uit document, maar item volgt nu met het scrollen en altijd relative t.o.v. het html element. (ideaal voor headers and footers)
- `position: sticky;` werkt alleen wanneer `top` of `bottom` property is meegegeven.
  - blijft enkel sticky binnen de parent.

### Flexbox
Geeft het element 2 assen om elementen op te plaatsen en de default as is horizontaal. Dus onze items worden niet meer boven elkaar getoond maar naast elkaar. 
Bijvoorbeeld:
```html
<div class="container">
  <div class="item item1">Item 1</div>
  <div class="item item2">Item 2</div>
  <div class="item item3">Item 3</div>
</div>
```
```css
.containter {
  display: flex;

  /*Verander de main axis: column , row (default)*/
  flex-direction: row;

  /*Hoe moeten de elementen geplaatst worden op de main axis: flex-start (default) , flex-end , center , space-between , space-around , space-evenly*/
  justify-content: flex-start;

  /*Hoe moeten de elementen geplaatst worden op de cross axis: flex-start (default) , flex-end , center , baseline*/
  align-items: flex-start;

  /*Hoe omgaan met overflow: nowrap (default) , wrap*/
  flex-wrap: wrap;
  /*Wrap unlocks align-content zelfde opties als `justify-content`*/
  align-content: flex-start;

  /*Plaats tussen elementen aanpassen: 0px (default)*/
  gap: 1em;
}

.item.item1{
  /* flex-grow: 1; */
  flex-shrink: 5; /*item 1 shrinkt 5x sneller dan de andere (0 item shrinkt niet)*/
  flex-basis: 300px; /*overschrijft breedte item in flex container*/
  
  /*SHORTHAND: grow shrink basis*/
  flex: 1;

  align-self: center; /*overschrijf align van container*/
  order: 2; /*verander de volgorde waarin elementen in flex container getoond worden t.o.v. volgorde in html bestand. (beter via HTML aanpassen)*/
}

.item.item2{
  flex-grow: 2; /*neemt dubbel zoveel plaats in als 1 en 3*/
}

.item.item3{
  flex-grow: 1;
}
```

<figure>
<img src="/img/flex-info.png" alt="drawing" style="max-height:40rem;"/>
<figcaption>Flex info <a href="https://medium.com/@MakeComputerScienceGreatAgain/understanding-flexbox-a-comprehensive-guide-992bcd5f04de">bron</a></figcation>
</figure>

### Grid
Met behulp van **Grid** kan je items plaatsen op basis van een soort grid coördinaten:
Bijvoorbeeld:
```html
<div class="container">
  <div class="item item1">Item 1</div>
  <div class="item item2">Item 2</div>
  <div class="item item3">Item 3</div>
</div>
```
```css
.containter {
  display: grid;
  /*Geef het aantal rijen en kolommen mee*/
  grid-template-rows: 100px 100px 100px; /*3 rijen van 100px*/
  grid-template-columns: 100px 100px 100px; /*3 kolommen van 100px*/

  grid-gap: 1em 2em; /*eerst gap tussen rows dan columns*/

  /*Naamgeving grid vakken*/
  grid-template-areas:
    'header header header'
    'vak4 vak5 vak6'
    'vak7 vak8 vak9';
}

/*Items positioneren*/
.item.item1{
  grid-row-start: 1;
  grid-row-end: 2;
  /*SHORTHAND*/
  /* grid-row: 1 / 2; */
  grid-column-start: 1;
  grid-column-end: 4;
  /*SHORTHAND*/
  /* grid-column: 1 / 4; */

  /*Via area naam*/
  /* grid-area: header; */

  z-index: 1; /*to show on top, if overlapping*/
}
.item.item2{
  grid-row: span 2;
  grid-column: span 2;
}

.item.item3 {
  grid-area: 2 / 3 / 4 / 4;
}
```

- **Extra items toevoegen**: Als je extra items toevoegd terwijl je grid al vol is, dan krijg je een impliciet grid dat automatische een rij bijmaakt bijvoorbeeld. Met `grid-auto-rows: 100px` kan je een grootte van `100px` geven aan de automatisch gegenereerde rijen (idem `grid-auto-columns`). Met de `grid-auto-flow` property kan je de overflow op `columns` toepassen i.p.v. `rows`.
- **Sizing van de templates**: Je kan alle gebruikelijke units gebruiken maar ook een extra speciale unit, specifiek voor grids: `fr`. Deze **fractional** unit gebruikt dan een fractie van de ruimte beschikbaar door de container. Je kan ook de `minmax()` functie gebruiken bv. `minmax(100px, 3fr)`
- **De `repeat()` function**: met de repeat functie kan je snel meerdere waarden herhalen bv `repeat(3, 100px)`
- **Gridception**: Je hoeft je niet te beperken tot 1 grid om je website op te stellen. Je kan een grid onder een grid, in een grid, naast een grid ... gebruiken. Think, trail, error, repeat is hier de boodschap.
- **justify-items en align-items**: deze properties werken gelijkaardig aan de flexbox, maar dan binnenin 1 vak van je grid. (`stretch` = default, `start`, `end`, `baseline`, `center`)
  - Gebruik de **justify-self** en **align-self** properties in de individuele elementen om ze apart te stijlen.
-**Plaatsing grid binnenin container**: Gebruik `justify-content` en `align-content` properties van de container. (`start` , `end` , `center` , `baseline` , `space-between` , `space-around`, `space-evenly`)

<figure>
<img src="/img/grid-info.png" alt="drawing" style="max-height:40rem;"/>
<figcaption>Grid info <a href="https://www.google.com/url?sa=i&url=https%3A%2F%2Fdev.to%2Fuzafar90%2Fa-comprehensive-guide-to-using-css-grid-for-creating-flexible-and-responsive-layouts-m4b&psig=AOvVaw0D3G40bRCDMYowZpINI_OO&ust=1709900931552000&source=images&cd=vfe&opi=89978449&ved=0CBIQjRxqFwoTCMCPn8KT4oQDFQAAAAAdAAAAABA5">bron</a></figcation>
</figure>

### Responsive grid zonder media queries
Example non responsive:
```css
.container {
  display: grid;
  grid-template-rows: repeat(4,100px);
  grid-template-columns: repeat(4, minmax(100px,1fr));
}
```
Example responsive:
```css
.container {
  display: grid;
  grid-template-rows: repeat(4,100px);
  /*Wrap item to next row if it doesn't fit*/
  grid-template-columns: repeat(auto-fit, minmax(100px,1fr));
}
```

## Easy dark mode example the right way
Een goede manier om een darkmode toe te voegen aan je website is gebruik te maken van CSS-variabelen voor je kleurenpallet. Dit is echter niet voldoende aangezien sommige HTML-elementen dan nog niet de correcte styling hebben. Hiervoor moeten we dus ook de color-scheme zelf aanpassen.
```html
html {
  color-scheme: dark light;
}
```

_Voorbeeld zie demo 7_

## UI design mock ups

Wanneer je start aan het ontwerp van je website is HTML-code schrijven niet de eerste stap. In de eerste plaats ga je nadenken **wat** je wil bereiken met je website, **wie** je wil bereiken met je website. Al je design keuzes moeten een antwoord geven op die vraag. In de eerste plaats ga je nadenken over wat de **content** van je website gaat zijn (wat moet er allemaal op mijn website beschikbaar zijn, wat zijn de functionaliteiten). Ten tweede ga je proberen die content op de beste manier te **presenteren**. Voor deze laatste stap kan je gebruik maken van een UI design tool. Daarmee kan je snel UI mock ups maken die je dan als referentie kan gebruiken bij het implementeren van je website. 

Bekende voorbeelden van zo een design tools zijn: [Figma](https://www.figma.com/ui-design-tool/) (free), [Sketch](https://www.sketch.com/pricing/), [Invision](https://www.invisionapp.com/), [Adobe XD](https://helpx.adobe.com/be_nl/xd/get-started.html), [Proto](https://proto.io/) ...


## Other practical responsive tips

- Gebruik container utility classes om je media queries simpel te structureren. (eventueel met max-width container gelijk aan min-width van query)
- Snapping vs resizing (eigen keuze)
- Maak `img` responsive met `display: block ; max-width: 100%;`
- Gebruik `min-height` i.p.v. `height` (idem `max-width` and `width`)
- Center met `margin-left: auto ; margin-right: auto;`
- Pas geen CSS aan dat niet nodig is. Zo behoud je het default gedrag opgelegd door de browser.
- `Width: auto;` werkt soms beter dan `width: 100%;`
- Zet de `flex-wrap` property op `wrap`, wanneer je een flexbox gebruikt.
- Vermijd media-queries waar mogelijk. (uitzondering bv. echt grote layout wijzigingen voor andere schermtypes)

## Simple interactive design example

_Voorbeeld zie demo 8_

## [Opdrachten](https://github.com/KULeuven-Diepenbeek/fsweb-demos-exercises-student/)

**Update je portfolio website**
1. Maak een keuze of je je website ontwikkeld voor desktop first of mobile first. En voeg de [clean slate code](#starting-fresh) toe bovenaan je CSS. (Pas nu dan eventuele padding/margins toe op de juiste plaatsen)
2. Bepaal 5 sizes waarvoor je media queries gaat schrijven en maak correct gebruik van min- of max-width op basis van je keuze in opdracht 1.
3. Gebruik flexboxes waar nodig.
4. Gebruik grid om de globale layout van je webpagina te bepalen. Tips voor layouts vind je [hier](/content/Frontend/html_basics.md#common-website-layouts)
5. Voorzie een checkbox en gebruik variables, CSS en javascript om een darkmode toe te voegen.
6. Animeer je navigatiebar en voeg en de checkbox toe om te switchen tussen light- en darkmode.
7. Experimenteer met een UI/UX tool. (Maak jouw ideale portfolio website in deze tool. Maak een mock up voor desktop en mobile)
<!-- https://www.figma.com/file/VqBqi98Ftkuts26CIMu3YH/Figma-basics?type=design&node-id=1669-162202&mode=design&t=rLbw3b4bzB9dkwnf-0 -->
