---
title: "Advanced CSS"
weight: 4
author: Arne Duyver
draft: false
---

_bron 1: Responsive Web Design with HTML5 and CSS - 4th edition - Ben Frain_</br>
_bron 2: [W3Schools](https://www.w3schools.com/html/default.asp)_

## Quicktip

Je css code spreiden over meerdere stylesheets is mogelijk door in je `main.css` andere CSS-bestanden te importeren. Je kan bijvoorbeeld al je animation klassen in een aparte `animations.css` plaatsen en dat bestand dan importeren met onderstaande code in je `main.css`:

```css
@import url('./animations.css');
```

**Moet helemaal in het begin van je CSS-file staan**

## Transformations
Met CSS-transformaties kun je elementen verplaatsen, roteren, schalen en scheef trekken. Volgende 2D-transformaties zijn beschikbaar:
- `translate()`
- `rotate()`
- `scaleX()`
- `scaleY()`
- `scale()`
- `skewX()`
- `skewY()`
- `skew()`
- `matrix()`

Examples:
```css
div {
  transform: translate(50px, 100px);
  transform: rotate(20deg);
  transform: rotate(-20deg);
  transform: scale(2, 3);
  transform: scale(0.5, 0.5);
  /* transform: scaleX(2);
  transform: scaleY(3); */
  transform: skew(20deg, 10deg);
  /* transform: skewX(20deg);
  transform: skewY(20deg); */
}
```

### Matrix
De methode `matrix()` combineert alle 2D-transformatiemethoden in één. De `matrix()` methode neemt zes parameters, die wiskundige functies bevatten, waarmee je elementen kunt roteren, schalen, verplaatsen (vertalen) en schuin houden.

De parameters zijn als volgt: `matrix(scaleX(), skewY(), skewX(), scaleY(), translateX(), translateY())`
```css
div{
  transform: matrix(1, -0.3, 0, 1, 0, 0);
}
```

## Transitions
Met **CSS-transitions** kun je de waarden van eigenschappen soepel veranderen gedurende een bepaalde tijd.
We bespreken de volgende overgang properties:
- `transition`
- `transition-property`
- `transition-duration`
- `transition-timing-function`
- `transition-delay`

Om een transition effect te maken, moet je twee dingen specificeren:
1. de CSS-property waaraan u een effect wilt toevoegen
2. de duur van het effect
_Opmerking: Als het onderdeel duur niet wordt opgegeven, heeft de overgang geen effect, omdat de standaardwaarde 0 is._

Om een transition te laten plaatsvinden, moet een element een verandering in state hebben en moeten voor elke state verschillende styles worden bepaald. De eenvoudigste manier om styles voor verschillende states te bepalen is door gebruik te maken van de `:hover`, `:focus`, `:active` en `:target` pseudo-klassen.

Example:
```css
div {
  width: 100px;
  height: 100px;
  background: red;
  transition: width 2s;
}
div:hover {
  width: 300px;
}
```

Je kan de transition properties apart definiëren of allemaal samen in een shorthand:
```css
div {
  transition-property: width;
  transition-duration: 2s;
  transition-timing-function: linear;
  transition-delay: 1s;
}
/* Shorthand */
div {
  transition: width 2s linear 1s;
}
```

Voor de transition-timing-function zijn er een aantal mogelijkheden. (**In de developper tools van je browser kan je met deze waarden spelen en de gewenste beziercurve kopiëren**):
- **`ease`** - specifies a transition effect with a slow start, then fast, then end slowly (this is default)
- **`linear`** - specifies a transition effect with the same speed from start to end
- **`ease-in`** - specifies a transition effect with a slow start
- **`ease-out`** - specifies a transition effect with a slow end
- **`ease-in-out`** - specifies a transition effect with a slow start and end
- **`cubic-bezier(n,n,n,n)`** - lets you define your own values in a cubic-bezier function

## Animations

Met **CSS-animations** laat je een element geleidelijk veranderen van de ene stijl naar de andere. Je kunt zoveel CSS-properties wijzigen als je wil, zo vaak je wil. 
We bespreken de volgende animation properties:
- `animation`
- `@keyframes`
- `animation-name`
- `animation-duration`
- `animation-delay`
- `animation-iteration-count`
- `animation-direction`
- `animation-timing-function`
- `animation-fill-mode`
- `animation`

Om CSS-animations te gebruiken, moet je eerst een aantal keyframes opgeven voor de animatie.
Keyframes geven aan welke stijlen het element op bepaalde momenten zal hebben.

```css
/* The animation code */
@keyframes example {
  from {background-color: red;}
  to {background-color: yellow;}
}

/* The element to apply the animation to */
div {
  width: 100px;
  height: 100px;
  background-color: red;
  animation-name: example;
  animation-duration: 4s;
}
```
Shorthand:
```css
div {
  animation-name: example;            /*keyframe name*/
  animation-duration: 5s;             
  animation-timing-function: linear;  /*linear, ease, ease-in, ease-out, ease-in-out*/
  animation-delay: 2s;                
  animation-iteration-count: infinite;
  animation-direction: alternate;     /*normal, reverse, alternate, alternate-reverse*/
  animation-fill-mode: forwards;      /*none, forwards, basckwards, both*/ 
}

/* Shorthand */
div {
  animation: example 5s linear 2s infinite alternate forwards;
}
```
**animation-timing-function**: zie `transitions`

**animation-direction**:
-**`normal`** - The animation is played as normal (forwards). This is default
-**`reverse`** - The animation is played in reverse direction (backwards)
-**`alternate`** - The animation is played forwards first, then backwards
-**`alternate-reverse`** - The animation is played backwards first, then forwards

**animation-fill-mode**:
-**`none`** - Default value. Animation will not apply any styles to the element before or after it is executing
-**`forwards`** - The element will retain the style values that is set by the last keyframe (depends on animation-direction and animation-iteration-count)
-**`backwards`** - The element will get the style values that is set by the first keyframe (depends on animation-direction), and retain this during the animation-delay period
-**`both`** - The animation will follow the rules for both forwards and backwards, extending the animation properties in both directions


### Verschil met transitions
**CSS-animations** definiëren complexe bewegingen met keyframes, zoals `rotate` of `fade`, terwijl **CSS-transitions** soepele veranderingen maken in elementeigenschappen, zoals grootte of kleur, tijdens gebeurtenissen zoals `hover`. Animaties gebruiken keyframes en kunnen oneindig herhalen, terwijl overgangen optreden bij eigenschapsveranderingen en meer geschikt zijn voor subtiele effecten. Beiden voegen interactiviteit en aantrekkelijkheid toe aan webpagina's, maar hebben verschillende toepassingen.

### Animation utility classes en dubbele classes
Je gebruikt **`animation utility classes`** om de relatie tussen animatie en element te ontkoppelen en voor herbruikbaarheid.

```css
.fade-in {
    animation: fadeIn 0.5s ease-in forwards;
}

@keyframes fadeIn {
    from {
        opacity: 0;
    }
    to {
        opacity: 1;
    }
}

@keyframes fadeAnimation {
  0%, 50%, 100%   {opacity: 1;}
  25%, 75%  {opacity: 0.5;}
}
```

Je gebruikt **dubbele klasse** notaties ter bescherming:

```css
.animatie.fade-in {
    animation: fadeIn 0.5s ease-in forwards;
}
```

### Animation play state
```css
div:hover {
  animation-play-state: paused; /*running, initial, inherit*/
}
```

### Animatie klasse dynamisch toevoegen met JavaScript
<!-- TODO -->

<!-- TODO in responsive
## Viewport
## Flexbox
## Grid
## Media queries
## aspect-ratio: 3 / 2;
## Images
## Videos
## Frameworks
## Templates -->

## [Opdrachten](https://github.com/KULeuven-Diepenbeek/fsweb-demos-exercises-student/tree/CSS-advanced-exercises)

**Exercise 1, 2, 3, 4**: Er worden drie `transitions` getoond. Je hebt ongeveer 8 minuten om de transities zo goed mogelijk te evenaren. Daarna wordt de oplossing overlopen. (De exacte pixel afstanden worden niet verwacht, het is voldoende wanneer de transitie gelijkaardig is).

**Exercise 5, 6, 7, 8**: Er worden drie `animations` getoond. Je hebt ongeveer 8 minuten om de animations zo goed mogelijk te evenaren. Daarna wordt de oplossing overlopen. (De exacte pixel afstanden worden niet verwacht, het is voldoende wanneer de transitie gelijkaardig is).

**Exercise 9, 10, 11, 12, 13, 14, 15**: Er worden 7 `transitions` of `animations` getoond. Je hebt ongeveer 15 minuten om de voorbeelden zo goed mogelijk te evenaren. Daarna wordt de oplossing overlopen. (De exacte pixel afstanden worden niet verwacht, het is voldoende wanneer de transitie gelijkaardig is).

### Portfolio website
Fleur nu je eigen portfolio website op door wat transitions en animaties toe te voegen waar nuttig.