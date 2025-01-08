Instalace: https://sass-lang.com/install
Dokumentace: https://sass-lang.com/documentation/
Playground: https://www.sassmeister.com/
***
### Spuštění automatického kompilování při změně souboru

```sh
sass --watch input.scss output.css
```

### Sledování veškerých změn v adresáři -> kompilování těchto změn do jiného adresáře

```sh
sass --watch app/sass:public/stylesheets
```

---
### Jednoduchá struktura Sass stylů
- \_base.sass - všechny resety, proměnné, mixiny, utilities
- \_layout.sass - řešení layoutu, container a grid systémy
- \_components.sass - všechno, co se dá použít víckrát (komponenta) - tlačítka, navbary
- \_main.sass - pouze importy z předešlých souborů
***
### Použití proměnných v jiném souboru
- `@import` už je zapovězené, použij `@use 'variables'`, soubor se jmenuje \_variables.scss 
- pro použití je potřeba jim přidat předponu z jakého souboru je beru
- `color: variables.$primary-color;`
- ještě je možnost `@use 'variables' as v;`
	- a mít `color: v.$primary-color;`
***
- Sass je superset CSSka -> to znamená, že veškeré .css soubory jsou taky validní .scss soubory
- vedle .scss souborů existují ještě .sass soubory, které jsou funkčně úplně stejné, ale liší se syntaxí
	- nemají středníky na konci řádku, jako konec řádku se bere newline
	- nemají složené závorky pro ohraničení bloků, používají odsazení

### Co Sass umí? Podrobnosti najdu v dokumentaci
- mohu pomocí speciálních komentářů rovnou tvořit dokumentaci ke stylům (viz http://sassdoc.com/)

### Proměnné
- o něco lepší a příjemnější management než v [[Proměnné v CSS]]
- platí jen ve scope, ve kterým je definuji (takže mám třeba nějaké globální proměnné, tak je mohu v rámci jednoho scope přepisovat)
	- níže je příklad použití, ale obecně platí, že bych globální proměnné měl mít uložené v jiném souboru a jen je includovat pomocí `@include` 
	- pojmenování podle standardu: "\_globals.scss"

```scss
$myColor: red;  
h1 {  
	$myColor: green; // zde přepíšu jen pro aktuální scope (tedy to neovlivní barvu 'p')
	color: $myColor;
}  
p {  
	color: $myColor;
}

// ----

h1 {
	$myColor: green !global; // tím jsem definoval $myColor globálně pro všechny scopes
}
```

### Vnořování
- lepší a přehlednější selectování elementů, tříd apod.
- dávej pozor na přílišné zanoření, pravidla závisení nech spíš volnější, aby se mohl (v rámci možností) měnit HTML kód bez změny stylů
	- velké zanoření pak generuje těžko čitelný CSS kód
- vnořování používám prakticky jen pro pseudotřídy (výhodou je že mám ty styly u sebe)
```scss
.element {
	&,
	&:hover,
	&:visited {
		padding: 20px;
	}
	// nebo
	&:hover {...}
	&:visited {...}
}
```

### Psaní @media queries
- lepší practice je psát @media queries přímo k elementu, kterého se to týká, je to sice trochu více psaní, ale ve výsledku je to přehlednější a lépe spravovatelné
```scss
h1 {
    margin-top: 60px;
    @media screen (max-width: 767px) {
        /* Mobile styles here */
    }
    @media (min-width:1025px) {
        /* Desktop styles here */
    } 
}
```

### Části (partials) a moduly
- v Sassu je super practice styly rozdělovat do partials, kdy každá část se věnuje stylování nějakého celku
- jednotlivé partials se pak naincludují do jednoho souboru, který se pak kompiluje do finálních CSS stylů
- syntax
	- každý partial musí začínat podtržítkem, např. `_partial.scss`
	- následné includnutí do jiného .scss souboru: `@use 'partial';

### Utilities
- "obecné styly", existují proto, abych spíš připisoval třídy do HTMLka než se opakoval v CSSku
```scss
.text-center {
	text-align: center !important; // u těhlech obecných utilitek většinou chci !important, protože pokud už ji k tomu elementu přidám, tak očekávám, že opravdu bude fungovat a jen tak si ji nepřepíšu
}

.mb-1 {
	margin-bottom: 0.2rem !important;
}

.mb-2 {
	margin-bottom: 0.5rem !important;
}
```
- dají se vygenerovat, příklad od Dominika [[Příklad generování utilities do Sassu|zde]] 

### Mixins
- části kódu, které chci opakovat na více místech ve stylech
- při jejich deklaraci lze předat hodnotu (chová se trochu jako funkce)

### Extend/Inheritance
- část kódu, který je společný pro nějaké prvky
- použití @extend => nemusím psát více tříd do HTML
- kouzlo je v překladu, kdy se kód nenakopíruje ke všem elementům, které jsem extendnul, ale vyselectují se všechny elementy a jim se jednou přiřadí společná část
- například mám společný kód pro tlačítka Approve, Reset, Quit
	- nastavím styly společné: velikost, padding, border atd.
	- a u jednotlivých tlačítek jen @extend a dopíšu třeba už jenom barvu textu a pozadí

### Matematika
`@use "sass:math"`
- mohu vypočítávat různé velikosti pomocí operátorů +, -, \*, math.div() a %