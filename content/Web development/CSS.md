[CSS-Tricks](https://css-tricks.com/) - web/blog s perfektním vysvětlením spousty věcí v CSS
[Vzhůru dolů](https://www.vzhurudolu.cz/)- kodérský český blog hlavně o CSS a správných postupech
[Flexbox Froggy](https://flexboxfroggy.com/) - hra na naučení se Flexboxu
[CSS Grid Garden](https://cssgridgarden.com/) - hra na naučení se CSS Grid
[Material Design](https://m3.material.io/) - Google open-source design guidelines
***
Full name: *Cascading Style Sheets*
[[HTML]] definuje strukturu dokumentu, CSS definují grafickou reprezentaci
Je tento způsob stylování podporován dostatečným počtem prohlížečů? -> https://caniuse.com/

- zajišťují dopřednou i zpětnou kompatibilitu
	- dopředná kompatibilita funguje tak, že pokud prohlížeč nepodporuje nějakou vlastnost, tak mi to nerozbije styly (tím způsobem, že to nezahlásí chybu a nezahodí to zbytek CSSka)

Není potřeba všechny styly psát do jednoho souboru, ale můžu do více souborů a pospojovat je `@import`
### Integrace do HTML
- nalinkování do hlavičky
	- `<link rel="stylesheet" type="text/css" href="styles/default.css">`
- nalinkování přes `@import`
```html
<style type="text/css">
	@import url("http://web.cz/styles/default.css");
</style>
```
- styly přímo v hlavičce (v tagu `<style>`)
- inline styly
	- hodí se při optimalizaci, rychlém načítání a vysoké prioritě, ale duplikuji a mám je roztroušené po celém dokumentu + při změně stylů musím editovat [[HTML]]
### Hodnoty
- Délky: pointy (pt) a pica (pc) se využívají ve velikostech fontů.
- `em` se vztahuje k písmenu M - je relativní k rodiči
- `rem` - je relativní k rootu
- `ex` se vztahuje k písmenu X
- `vh a vw` - relativní k výšce a šířce zařízení

Ideální je vůbec nepoužívat px. Stačí mi jenom relativní jednotky. Absolutní slouží k tisku např. abych to mohl napozicovat správně. Můžu mít také speciální CSSka pro projektor (kde mám taky fixní šířku a výšku).

Elementy nedědí relativní hodnoty, ale hodnoty již vypočtené.
### Písmo
`sans` patkové písmo
`sans-serif` bezpatkové písmo
### Selektory
- specificita selectorů: [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity) 
	- když budu chytře využívat specificitu, tak nemusím pak tolik používat `!important`
- [Chytrý cheatsheet zde](https://frontend30.com/css-selectors-cheatsheet/) - skrz cvičení se můžu tak potrénovat, co všechno se vlastně selectne
![[Pasted image 20240106110850.png]]
- `:lang(cs)` se bere z HTML `<html lang="cs">`
- `div > p` -> vyber všechny p elementy, které mají div jako přímého rodiče
- vedle selektoru následníka `E + F` (= adjacent sibling selector) existuje ještě general sibling selector `E ~ F`, který vybere všechny elementy F, které jsou na stejné úrovni jako E 
	- zatímco s `+` to vybere pouze jeden element F, které následuje za E (a mají pořád stejného rodiče a jsou na stejné úrovni)
- lze psát také `p:nth-child(2n+1)` nebo `p:nth-child(odd|even)` -> [MDN here](https://developer.mozilla.org/en-US/docs/Web/CSS/:nth-child)
- existuje i `p::first-line`

Nepsat text capslockem (velkými písmeny) do HTML, spíš to pak upravit v CSSku (`text-transform: lowercase/uppercase`).
### Dědičnost
- hodnota stylu je buď původní, zděděná (z rodiče), jako výsledek z pravidel
- styly může definovat: autor, uživatel stránky (např. v dev-tools, přepnutím do darkmode), prohlížeč (User-Agent)
### Kaskáda - určení pravidla
1) najdi všechny deklarace, které se vztahují k danému elementu
2) seřaď je podle důležitosti
	1) UA < user < author < author !important < user !important
		1) uživatelé si mohou definovat svoje styly pro prohlížení svých stránek (není to tak běžné, ale dělá se to pro přístupnost a osobní preference)
3) v případě rovnosti, seřaď podle specificity selektoru
4) v případě další rovnosti, seřaď podle pořadí ve kterém byly specifikovány
### Specificita [[CSS#Selektory|Selektorů]]
- v "binárním" čísle - čím vyšší číslo, tím vyšší specificita
![[Pasted image 20240108101613.png]]
![[Pasted image 20240108101641.png]]
### CSS 3
- přidává media queries
- nové selectory (`:is()`, `:not()`, `:where()`)
- nové animace, transformace
- flexbox, grid apod.
### Frameworky
- obecné (Bootstrap, Bulma, Foundation)
- utility (Tailwind, Materialize)
- lightweight (Pure, Miligram, Skeleton)
- další specializované
- [[Rapid application development#CSS|Class-less framework přístup]]