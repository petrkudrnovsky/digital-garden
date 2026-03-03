```js
document.head;
document.body; // body element
document.documentElement; // takhle si sáhnu na html element
```

```js
document.getElementById()
document.querySelector('#user')
document.querySelectorAll() // vrátí všechny elementy vyhovující selektoru
	// vrátí také statickou kolekci (takže když se DOM změní, tohle zůstane stejné)
document.getElementsByTagName('div')

```
Object
EventTarget
Node
HTMLElement
HTMLHeadElement

### document.body.children vs. document.body.childNodes?
- `children` mi dá array children elementů
- `childNodes` mi dá Nodelist array všech nodes, takže i textu a comment nodes

const divs = document.getElementsByTagName('div');
const divs = Element.children;
- toto je tzv. "živá" kolekce, do divs se nic neuloží, až když se budu snažit accesnout divs (např. divs\[2\]), tak se projede aktuální strom a vrátí mi to aktuální stav
- třeba .querySelectorAll() vrátí statickou kolekci
### Element Dataset [MDN](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dataset)
- pomocí JS si mohu k jednotlivým elementům ukládat informace pomocí `data-...` atributu
- také na ně fungují metody `element.getAttribute("id")` nebo `element.setAttribute("id", 1)`
	- speciální chování pro atributy bez `value`: `button.setAttribute("disabled", "");`
```js
const inp = document.querySelector('input');
console.log(inp.dataset.mojeKocicka);

// <input data-moje-kocicka="2"></input>

Array.from(inp.attributes).forEach(...) // muzu si vypsat vsechny atributes jako text
```

==nepoužívat .innerText, ale .textContent== 

### Jak zapojit skript do HTML stránky?
```html
<!-- v <body> -->
<script></script>
```
- toto je synchronní imbeddování skriptu do stránky
- generování stránky se zastaví, dokud skript není hotový (až bude mít prázdný callstack)
	- řešení: dát skript tag na konec stránky (takže se spustí až na konci generování stránky)
```html
<script async src="..."></script>
```
- stahuje se asynchronně <- výhoda
	- nevýhoda je, že pokud takových `async` skriptů mám více pod sebou, tak se negarantuje pořadí, tedy 2. skript může záviset na něčem z prvního a je problém
```html
<script defer src="..."></script>
<script defer src="..."></script>
<script defer src="..."></script>
```
- tady se skripty stahují a vykonávají asynchronně a zároveň se zachovává pořadí vykonávání
- `<noscript>` sem hodím HTML obsah, který se zobrazí, pokud má uživatel vypnutý Javascript v prohlížeči
	- hodí se, pokud je stránka závislá na Javascriptu a nebude fungovat dobře
	- platí, i když používám nějaký JS framework

### Vytváření elementu
- funkcí `document.createElement('div');` 
- poté zapojení do DOMu: `parentEl.appendChild(childEl)` - vloží ho na konec childů parenta
- metoda pro přidání kamkoliv mezi childy parenta: `parent.insertBefore(coChciVlozit, predCoChciVlozit)` 
	- "švýcarský nůž na vkládání"
	- `insertBefore(div, null)` - vloží na konec (podobně jako appendChild)
### .innerHTML, .insertAdjacentHTML()
- když chci rychle dát nějaký element (i s nějakými atributy), tak je to rychlé řešení
- nevýhoda: když opakovaně zapisuju do innerHTML, tak se stahuje a renderuje znovu a znovu (ten, co je tam předtím) - a když jsou tam třeba velké obrázky, tak je to celkem neefektivní
	- když dělám `.innerHTML += ...`

	- `parent.insertAdjacentHTML('beforeBegin', content)
		- content je string, který chci vložit (např. `<p>...</p>`)
		- konstanty: 
			- beforeBegin
			- afterBegin
			- beforeEnd
			- afterEnd
### Smazání elementu
- `removeChild(childNode)` #to-do
### Kopírování elementu
- `cloneNode()` - překopíruje jen ten element, na který ukazuje
- `cloneNode(true)` - překopíruje i včetně children
### Optimalizace
- Když mám více elementů na vložení, tak je lepší si je dát stranou dohromady a do stránky je vložit najednou, je to lepší než to dělat postupně
- Místo balení do `div` navíc pro vložení najednou můžeme použít [[#Fragment]] 
### Fragment
- "host" element, který si můžu v JS vytvořit, navkládat do něj nějaké elementy a pak to celé dát do stránky, ale samotný obal (=fragment) zmizí, nebude tam
- `createDocumentFragment()`
### Capture X bubble fáze
- když kliknu na tlačítko, tak událost kliknutí propadá přes jednotlivé elementy (body->form->button), a pak vybublává zpátky (button->form->body)
	- a u každého elementu se zastaví a říká mu: já jsem takový event a jsem v takové fázi a pokud na tom jsou navěšené nějaké události, tak se provedou
### Události
- registrovat v podstatě jen přes addEventListener - tím způsobem můžu přidat více eventHandlerů
- lze zastavit propagaci eventu dále (pro obě fáze najednou)
	- `event.stopPropagation()`
- preventDefault
- registrovat na document::DOMContentLoaded
	- kdy už byl [[HTML]] dokument rozparsován
	- `defer` skripty byly načteny a spuštěny
	- nečeká se na `async`
	- vhodné pro registraci událostí
- dá se také registrovat na window::load
	- veškerý obsah (vč. obrázků, videí...) byl načten
	- vhodné pro spuštění dynamického obsahu závislého na všech zdrojích