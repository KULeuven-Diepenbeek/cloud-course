---
title: "HTML"
weight: 1
author: Arne Duyver
draft: false
---

_bron 1: Responsive Web Design with HTML5 and CSS - 4th edition - Ben Frain_</br>
_bron 2: [W3Schools](https://www.w3schools.com/html/default.asp)_

## HyperText Markup language
HTML staat voor 'HyperText Markup Language' en is een manier om content te markeren zodat het door een technologie begrepen kan worden. HTML is essentieel voor menselijk begrijpbare webcontent. Je markeert tekstinhoud met tags/elements om structuur aan te brengen.

### Elements, Tags en Attributes
Een **HTML element** wordt gedefiniëerd door een openingstag, elementinhoud en een sluitingstag. Een **HTML tag** wordt weergegeven met de naam van het element binnenin "< ... >" bv. `<p>`. Een sluitingstag bevat nog een "/" voor de element naam bv. `</p>`. Elke openingstag **moet** meestal ook gevolgd worden door een sluitingstag van hetzelfde element bv. `<p> ... </p>`. Een uitzondering op deze regel zijn een aantal **self-closing elements** die geen elementinhoud bevatten. Een self-closing element bevat dus maar één tag waarin de "/" na de elementnaam komt en voor de sluitende ">" bv. `<img src="/img/html_syntax.png"/>` of `<br/>`.

<figure>
<img src="/img/html_syntax.png" alt="drawing" style="max-height: 10rem;"/>
<figcaption>HTML syntax and structure of an HTML element <a href="https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.codingame.com%2Fplaygrounds%2F8240%2Fhtml-syntax-for-beginners&psig=AOvVaw3mywD2Isv3nxKmiqCebZu9&ust=1709383397838000&source=images&cd=vfe&opi=89978449&ved=0CBIQjRxqFwoTCLi7jcWL04QDFQAAAAAdAAAAABAD">bron</a></figcation>
</figure>

Een element kan verschillende **attributes** bevatten die extra informatie over een HTML element bevatten. Deze attributen kunnen het gedrag of uiterlijk van een element wijzigen, de functionaliteit ervan definiëren of andere details specificeren, zoals de relatie met andere elementen of het gedrag als reactie op gebruikersinteracties. 
<br/>Attributen worden aan HTML-elementen toegevoegd met behulp van _name-value pairs_ binnen de openingstag van het element. Enkele veelgebruikte HTML-attributen zijn: `href`, `src`, `style`, ...

Op volgende manier gebruik je attributen in HTML tags (Een element kan meerdere attributen bevatten en je zelf ook eigen attributen toevoegen):
```html
<p attributeName="attributeValue">...</p>
```

Twee belangrijke attributen zijn '`id`' en '`class`' die je helpen een specifiek element terug te vinden met behulp van CSS-selectors of JavaScript. 
```html
<p id="paragraaf1" class="specialeParagraafKlasse" value="1">...</p>
```
><i class="fa fa-info-circle"></i> Een element kan maximaal één `id` hebben maar wel meerdere `class` namen.

Een element kan meerdere andere elementen bevatten. Hier spreken we dan van **nested elements**, bv.:
```html
<p id="paragraaf1" value="1">
    <h1>...</h1>
    <a>...</a>
</p>
```

### Structuur van een HTML-document
Je kan eender welke simpele tekst- of code-editor gebruiken om HTML bestanden aan te maken of te bewerken (bv. notepad, notepad++, vscode, sublime text, atom, vim, nano ...). Een HTML bestand zou volgende hoofdstuctuur moeten volgen:
```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="utf-8" />
        ...
    </head>
    <body>
        ...
    </body>
</html>
```

- **Doctype**: Hiermee geven we weer welk type dit document is. In ons geval dus steeds html.
- **html element**: Het HTML element met de naam 'html'. Dit wordt de **root tag** van het HTML bestand genoemd, want alle html code moet binnen de `<html>` en `</html>` tags staan
    - **lang attribuut**: Met het language attribuut geven we meer informatie mee aan de webbrowser door te vermelden dat de content op deze webpagina in het Engels (`en`) geschreven is. (Nederlands =  `nl`)
- **head element**: dit is een container voor de volgende metadata: `<title>`, `<style>`, `<meta>`, `<link>` and `<base>`. (eventueel ook `<script>`)
    - **meta element**: wordt gebruikt om de karakterset/encoding, paginabeschrijving, trefwoorden, auteur van het document en viewport settings te declareren.
        - **character encoding**: dit verteld de webbrowser hoe de karakters gecodeerd zijn zodat hij ze correct kan decoderen. Tenzij je een goede reden hebt is de gebruikte codering normaal `utf-8`.
- **body element**: een element container die al de overige inhoud van het HTML bevat. De headers, paragrafen, de tekst ...

### Types van HTML elements
- **Sectioning elements**: elementen die worden gebruikt om de structuur van een webpagina te definiëren door secties van inhoud te scheiden door de verschillende delen semantische betekenissen mee te geven. Bv. `<header>` en `<footer>`.
- **Grouping elements**: elementen die worden gebruikt om meerdere inhoudsitems te groeperen of te bundelen onder één overkoepelend element. Bv. `<div>` en `<span>`.
- **Text-level semantics**: dit verwijst naar de manier waarop HTML elements de betekenis en structuur van tekst op een webpagina definiëren. Deze elementen worden gebruikt om specifieke delen van de tekst te markeren en hun semantische betekenis aan te geven. Bv. `<em>` en `<strong>`.

### List of usefull elements (by type)
```html
<!-- This is a comment in HTML -->

<!-- SECTIONING ELEMENTS -->

<nav>
    The 'nav' element is used to mark up a collection of links to external pages or sections within the current page. As well as being used for the main website navigation, the 'nav' element is also a good fit for things like a table of contents, or a blogroll.
</nav>

<header>Header for webpage</header>

<aside>
    The 'aside' element is used to represent content that is tangibly related to the content surrounding it, but could be considered separate. This includes things like sidebars
</aside>

<main>
    The 'main' element should contain the main content for your web page. All of this content should be unique to the individual page, and should not appear elsewhere on the site. Any content that is repeated on multiple pages (logos, search boxes, footer links, etc.) should not be placed within the 'main' element.

    You should only use one 'main' element on a page, and it shouldn’t be placed within an 'article', 'aside', 'header', 'footer', or 'nav' element.

    <article>
        <header>
            the 'header' element is used to represent the introductory content to an article or web page. This will usually contain a heading element as well as some metadata that’s relevant to the content, such as the post date of a news article for example.

            <h1>Largest Header</h1>
            <h2>Header 2</h2>
            <h3>Header 3</h3>
            <h4>Header 4</h4>
            <h5>Header 5</h5>
            <h6>Smallest Header</h6>

        </header>

        The 'article' element should contain a piece of self-contained content that could be distributed outside the context of the page. This includes things like news articles, blog posts, or user comments.

        You can nest 'article' elements within one another. In this case it’s implied that the nested elements are related to the outer 'article' element.

        <aside>Tangibly related content</aside>
    </article>

    <section>
        The 'section' element is used to represent a group of related content. This is similar to the purpose of an 'article' element with the main difference being that the content within a 'section' element doesn’t necessarily need to make sense out of the context of the page.
        
        It’s advisable to use a heading element ('h1' – 'h6') to define the topic for the section.

        If you just need to group content together for styling purposes you should use a 'div' element rather than a 'section'

        <footer>
            The 'footer' element is used to represent information about a section such as the author, copyright information, or links to related web pages.
            <address>
                This element is not for marking up postal address, but rather for representing the contact information for an article or web page. This could be a link to the author’s website or their email address.
            </address>
        </footer>

    </section>

    Based on content and design, articles can contain sections and/or sections can contain articles.
    
</main>

<footer>
    Footer for webpage
    <address> Link to email author webpage </address>
</footer>


<!-- GROUPING ELEMENTS -->
<div>
    Used for grouping blocks for easy styling. <span>Used for grouping inline content</span>
</div>

<p>Defines a paragraph</p>

<pre>Defines preformatted text</pre>

<blockquote cite="citation_source">
    Specifies a section that is quoted from another source
</blockquote>
<q>For inline (short) quotations</q>

<ol type="I" start="1"> <!-- type="1|a|A|i|I" -->
    <!-- Defines an ordered list. An ordered list can be numerical or alphabetical. -->
    <li>List Item I</li>
    <li>List Item II</li>
    <li>List Item III</li>
</ol>
</ul>
    <!-- Defines an unordered list. Used the same a 'ol' -->
</ul>

<table> <!-- Defines an HTML table -->
  <tr> <!-- Defines a table row -->
    <th>Defines a table head for column</th> <!--  -->
    <th>Header column 2</th>
  </tr>
  <tr>
    <td>Defines a table cell</td>
    <td>Cell row 2, column 2</td>
  </tr>
  <tr>
    <td>Cell row 3, column 1</td>
    <td>Cell row 3, column 2</td>
  </tr>
</table>

<figure>
    <img src="img_source_web/or/local_file.png" alt="alternative text if img src not found" width="auto" max-width="500px"/>
    <!--src="./voorbeeld/realtive/path/img.jpg | C://voorbeeld/absolute/path/img.jpg | https://www.voorbeeld-url.com/img.jpg" -->
    <figcaption>Image caption</figcation>
</figure>

<!-- TEXT-LEVEL SEMANTICS -->

All simple text is displayed without line breaks.
But 'br' creates
<br/> a line break.<br/><br/>

<a href="link_to_webpage/or/local_file" target="_blank">Link text<a> <!-- target="_blank | _self | _parent | _top" -->
<!--href="./voorbeeld/realtive/path | C://voorbeeld/absolute/path | https://www.voorbeeld-url.com" --><br/><br/>

The <em>em-tag</em> is used to define emphasized text. The content inside is typically displayed in italic.<br/><br/>

The <i>i-tag</i> defines a part of text in an alternate voice or mood. The content inside is typically displayed in italic.<br/><br/>
<!-- Use the <i> element only when there is not a more appropriate semantic element -->

The <strong>strong-tag</strong> is used to define text with strong importance. The content inside is typically displayed in bold.<br/><br/>

The <b>b-tag</b> specifies bold text without any extra importance.<br/><br/>

The <small>small-tag</small> defines smaller text (like copyright and other side-comments).<br/><br/>

The <s>s-tag</s> specifies text that is no longer correct, accurate or relevant. The text will be displayed with a line through it.<br/><br/>

The <cite>cite-tag</cite> defines the title of a creative work (e.g. a book, a poem, a song, a movie, a painting, a sculpture, etc.).<br/><br/>

<p>
The <dfn>dfn-tag</dfn>  stands for the "definition element", and it specifies a term that is going to be defined within the content.
</p>

The <abbr title="abbreviation">ABBR</abbr>-tag defines an abbreviation or an acronym.<br/><br/>

The <time datetime="2024-02-18 19:00">time-tag</time> defines a specific time (or datetime).<br/><br/>

The <code>code-tag</code> is used to define a piece of computer code. The content inside is displayed in the browser's default monospace font.<br/><br/>

The <var>var-tag</var> is used to defines a variable in programming or in a mathematical expression. The content inside is typically displayed in italic.<br/><br/>
TODO

<!-- FORMS AND INPUTS -->
<form>
    fieldset
    label
    input text
        type number, password,email, radio, checkbox, submit, button, file (accept="image/png, image/jpeg")
        Placeholder
        value
        name
        id
    radiobutton
    checkbox
    dropdown met select en option (met value attr)
    textarea
</form>

<!-- EXTRA -->
-button
-symbols
-Details + summary
-icons


```
[bron1](https://www.w3schools.com/tags), [bron2](https://blog.teamtreehouse.com/use-html5-sectioning-elements), [bron3](https://www.w3.org/WAI/ARIA/apg/patterns/landmarks/examples/HTML5.html)

### Een voorbeeld mappenstructuur voor je webpagina's
```
root/
|
+---index.html
|
+---html/
    |
    +--- about/
    |    |
    |    +--- about.html
    |
    +--- contact/
    |    |
    |    +--- contact.html
    ...
+--- assets/
    |
    +--- images/
        |
        +--- image1.png
...
```

## Common website layouts

<figure>
<img src="/img/common-website-layouts.jpg" alt="drawing" style="max-height: 46em;"/>
<figcaption>4 Common website layouts with HTML-elements <a href="https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.ecurtisdesigns.com%2Fweb-layout-design%2F&psig=AOvVaw1sO2aRTXVQnLjp0IMXVzpu&ust=1709382800202000&source=images&cd=vfe&opi=89978449&ved=0CBIQjRxqFwoTCMjp4KWJ04QDFQAAAAAdAAAAABAa">bron</a></figcation>
</figure>

### HTML vs HTML5

HTML5 is de nieuwere versie van HTML met een aantal nuttige voordelen. Zo is HTML5 zeer vrijgevend gezind in het weglaten van bepaalde attributen en gebruikt HTML5 default waarden voor attributen die nodig zijn maar niet specifiek uitgetyped werden. W Bijvoorbeeld:

```html
<link href="CSS/main.css" rel="stylesheet" type="text/css" />
```
HTML5:
```html
<link href=CSS/main.css rel=stylesheet >
```
Merk op dat in het HTML5 voorbeeld geen "/" heeft voor het sluitende groter dan teken, geen quotes gebruikt voor de value van het attribuut 'href' en 'rel' en er geen attribuut 'type' gedefinieerd is. HTML5 zal hier echter niet moeilijk over doen.  

> <i class="fa fa-exclamation-circle" aria-hidden="true"></i>
> In het algemeen is het wel een goede strategie om je HTML bestand zo specifiek mogelijk te coderen om problemen in een later stadium te vermijden.

Aangezien HTML5 dus gewoon de nieuwere versie is van HTML gaan we dit gebruiken.

## Opdrachten
1. Maak een HTML-bestand en noem het portfolio.html
2. Geef de titel van je webpagina de naam "<naam> portfolio".
3. Voeg jezelf toe als auteur van de webpagina
4. Gebruik het header element om de onderstaande structuur aan te brengen aan je webpagina.
    - Expertise
    - Over mij
    - Mijn projecten
    - Technische vaardigheden en CV
    - Contact
5. Gebruik de relevante html elementen om het volgende toe te voegen aan je webpagina: voeg boven de header expertise een welkomstbericht toe. (Voor inspiratie voor het tekstje kan je ChatGPT gebruiken, de vormgeving doe je ZELF). Emphasize een aantal inspirerende woorden door ze in het vetgedrukt/schuin te zetten.
6. Voeg ook een mooie afbeelding toe die je online ophaalt. Gerbuik hiervoor het 'figure' element en voeg een caption toe
7. Plaats de welkomsttekst binnenin een div en geef die div de klassenaam 'welcometext'.
8. Geef in de Expertise sectie een lijst waarin je je eigen vaardigheden in de verf zet. (je kan ook inspiratie opdoen op andere portfolio websites).
9. Plaats in de Expertise sectie ook een link naar de sectie Technische vaardigheden en CV
10. Plaats in de 'Over mij' je favoriete quote van je lievelingsfilm/-boek in de Over mij sectie. Gerbuik hier het juiste element voor.
11. Geef ook wat meer informatie over jezelf en plaats minstens één belangrijke zin in een span en geef die de id 'important-sentence'.
12. Plaats hier ook een afbeelding (de afbeelding moet je lokaal hebben staan)
13. Maak onder de sectie 'Mijn projecten' subsecties voor alle projecten die je al eens gemaakt hebt. Bijvoorbeeld je project van ELSY van het eerste jaar, je PES project, eigen andere projecten …
14. Onder technische vaardigheden maak je een tabel met je verschillende opleidingen in (naam opleiding, startjaar, eindjaar). Voeg ook een lijst met beheerste talen toe en link hier ergens naar  volgende webpagina: [https://detaalbrigade.nl/taalniveaus/](https://detaalbrigade.nl/taalniveaus/ )
15. Breng wat meer structuur aan in je teksten met divs, paragrafen en line breaks. Voeg ook eens wat symbolen via hun htmlcode toe waar nuttig. Gebruik verschillende achtergrond kleurtjes om te bekijken hoe de verschillende structuren werken (doe dit ook voor je span).
16. Gerbuik inputs boxen, knoppen, checkboxen, … om een contactformulier aan te maken. Je vindt hier ontelbare voorbeelden van op het internet. 
17. Neem nu je 'Contact' sectie en plaats die in een nieuw bestand genaamd 'contact.html' en plaats deze in de subfolder genaamd 'contact'.
18. Maak footer aan waarmee je navigeert naar je 'contact' page van je website.
19. Voeg in je head een link toe naar fa-icons zodat je die icoontjes kan gebruiken: 
<br/>`<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">`
20. Voeg aan je footer ook een link toe via een icoon naar je Github profiel en een icoon voor je linkedIn profiel (indien je dat hebt)