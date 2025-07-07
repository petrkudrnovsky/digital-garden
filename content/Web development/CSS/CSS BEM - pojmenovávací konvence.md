Zdroj: https://www.vzhurudolu.cz/prirucka/bem
***
- způsob psaní znovu-použitelných komponent a neztratit se v tom
- jsou 2 verze BEMu
	1) plnohodnotné organizování CSS, viz [bem.info](https://en.bem.info/methodology/)
	2) konvence pro pojmenovávání tříd - za mě lepší, můžu mít vlastní organizaci CSS stylů, ale zároveň se v tom kvůli specifickému pojmenování neztratím
- BEM rozšiřuje [[OOCSS - objektové CSS]] - to je hlavně o komponentové části kódu, BEM je hlavně pojmenovávací konvence
- tato poznámka je hlavně o té pojmenovávací konvenci

## BEM = Blok, Element, Modifikátor

| Typ třídy | Způsob pojmenování |
| ----------- | --------------- |
| Blok | .block |
| Element | .block__element
| Modifikátor | .block--modifier
| Modifikátor elementu | .block__element--modifier

```html
<nav class="nav nav--secondary nav--visible">
```
- zde je nav block (tedy samostatná komponenta) a nav--secondary a nav--visible jsou její modifikátory a všechny je pravděpodobně najdu v souboru nav.css či nav.scss ([[How To Sass]])

### Blok
- = komponenta uživatelského rozhraní, nezávislý prvek, který lze znovupoužívat v jiných projektech
- zápis: `.button, .main-nav`
	- bloky s více slovy odděluji jedním podtržítkem

### Element
- jedná se o prvek uvnitř bloku, který nějakým způsobem potřebuju nastylovat
- nelze ho použít samostatně, dává smysl jen uvnitř bloku
- zápis: `.nazev-bloku__nazev-elementu`

### Modifikátor
- varianta komponenty (bloku), když si ho potřebuji pro dané místo upravit
- ve svém jméně má vizuální vlastnost (`.block--small`), či svůj stav (`.block--disabled`) nebo svoje chování (`.block--animated-to-left`)
	- v názvu modifikátoru by nemělo být nic závislého na vzhledu (`.block--green`)

### Modifikátor elementu
- u složitějších stylů je také potřeba modifikovat elementy v rámci jednoho bloku (komponenty)
- zápis: `.nazev-bloku__nazev-elementu--nazev-modifikatoru`

## Struktura stylů v BEMu
- nejdřív styluju blok/element a ihned pod nimi styluju jejich modifikátory (varianty)
- tím se vyhnu složeným selektorům a mám nízkou váhu selektorů (viz [[OOCSS - objektové CSS#3) Co nejnižší specifičnost]])
- ==výjimka==
	- selektor o dvou třídách mohu použít v situaci je stylování elementu v modifikovaném bloku
	- je to hlavně z praktických důvodů - element je totiž vždy závislý na svém rodiči a pokud bych ho chtěl mít samostatně, tak bych musel všude ještě všem elementům v rámci toho modifikovaného rodiče přidávat další modifikátor: `.nav__item--secondary`
```css
.nav--secondary .nav__item {
  /* Měnící se pravidla položky navigace pro sekundární navigaci */
}
```
## Rady od Martina Michálka
- šetři se zanořováním v preprocesorech jako je [[How To Sass]]
```css
/* Tohle raději ne: */
.nav {
  &__item { }
  &--secondary { }
}
```
- blbě se v tom vyhledává a pokud to přeženu, tak to vede k nepřehlednosti a k závorkovému peklu

## BEM a Bootstrap
- Bootstrap je objektově napsaný, ale nemá pojmenovávací konvenci jako BEM
- takže pokud Bootstrap převažuje v projektu, tak se na BEM vykašli a řiď se konvencí Bootstrapu
- pokud převažuje moje vlastní CSSko, tak bych svoje komponenty měl odlišit třeba nějakým prefixem
	- např. Bootstrap bude mít `.table`, tak já budu mít `.mp-table` 
		- prefix mohu zvolit na základě názvu projektu, to už je na mně