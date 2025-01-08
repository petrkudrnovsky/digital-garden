Zdroj: https://www.vzhurudolu.cz/prirucka/oocss

- OOCSS mi pomáhá psát CSS styly tak, abych je moje snadno spravovat a používat na dalších místech (klidně i dalších projektech)
### Klíčové slovo: komponenta
- komponenta je nějaký prvek na stránce, která může být abstrahována 
- nejlépe to popíše následující příklad s tlačítkem:
```css
/* Komponenta */
.button { … }

/* Elementy komponenty */
.button-icon { … }

/* Modifikátory komponenty */
.button-primary { … }
.button-login { … }
```

# 5 hlavních principů OOCSS

## 1) Nezávislost vzhledu na struktuře
- HTML tagy do CSS NEpatří - při jejich změně nechceme dělat změny jak v HTML, tak i v CSS
```css
/* Tohle je v objektovém CSS zakázáno: */
button { … }
input.button { … }
.comment img { … }
```
- jako výjimku bereme stylování základní typografické vrstvy projektu - tam selectory `p` či `h1` mohu v pohodě použít
## 2) Nezávislost obsahu na kontejneru
- do CSS Selektorů nechceš promítat strukturu DOMu, ta se také může měnit
```css
/* Špatně: */
.login-form .button { … }

/* Dobře: */
.button { … }
.button-login { … } /* modifikátor */
```
## 3) Co nejnižší [[CSS#Specificita CSS Selektory Selektorů|specifičnost]]
- v CSS nechceš používat selectory identifikátorů (`#id`) kvůli jejich vysoké specifičnosti
	- `id="..."` si nech pro kotvy či referencování z JS
- `!important` si taky nech - to používej pro debuggovací, či vysoce specifické účely
- nepiš zbytečně selektory rodičů (už je to zase kopírování struktury DOMu)
- nepiš kombinované selektory, např. `.button.button-primary`
- [O specifičnosti na MDN - lidsky](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity)
- [Kalkulačka specificity](https://specificity.keegan.st/)
## 4) Vývoj zaměřený na komponenty a znovupoužitelnost
- komponenta = objekt
- jsou nezávislé a lze je pak používat na jiných projektech
- tvoří pak samostatný celek, který pak importujeme
```css
@import "button.css";
@import "nav.css";
```
## 5) Princip prefixování: Objekt, Element, Modifikátor
- už trochu souvisí s [[CSS BEM - pojmenovávací konvence]]

- tipy od Martina Michálka
	- není potřeba striktně využívat objektový přístup se vším všudy a lpět na tom
	- ale alespoň základy principů bych měl používat všude
		- hlavně snížení specifičnosti, nezávislost vzhledu na struktuře a obsahu na kontejneru