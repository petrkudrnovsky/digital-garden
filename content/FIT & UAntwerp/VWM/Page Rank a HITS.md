Motivace pro zavedení algoritmu Page rank - protože při [[Boolský vyhledávací model]] a [[Vektorový vyhledávací model]] samotným jsou všechny dokumenty stejně důležité (důvěryhodné) a scammeři mohou dokumenty upravovat tak, aby se matchovali na co nejvíce vyhledávacích dotazů - je potřeba nějak ohodnotit popularitu a důvěryhodnost stránky

Nezáporné reálné číslo udávající popularitu a důvěryhodnost webové stránky.
- to ovlivňuje hodně faktorů
	- na jaké stránky odkazuji a jaké stránky odkazují na mě
	- sociální kontext společnosti a autora

Na rankování stránek existují 2 hlavní algoritmy: Page Rank (Google) a HITS.

Rankování stránek probíhá tak, že se nejdříve jednotlivé documenty (např. webové stránky) ohodnotí podle obsahu (např. podle kosinové vzdálenosti a vektorového retrieval modelu) a poté re-ranknutí těchto výsledků agregací s page rank skórem pro jednotlivé stránky.

Mezi jednotlivými stránkami se nachází odkazy, máme několik typů stránek:
- hub - hodně outlinků
- authority - hodně inlinků
- oboje

### Page Rank
>A web page is important if it is pointed to by other important pages.
- zjednodušeně je page rank pro stránku součet podílů page ranku všech stránek $v$, které na stránku $u$ ukazují, vydělený celkovým počtem odkazů na stránce $v$
	- ![[Pasted image 20250525103736.png]]
	- $d$ je tzv. *damping factor*, je to pravděpodobnost, že se klikne na náhodný odkaz na stránce
		- díky tomu se systém nezacyklí na malých skupinách stránek a také izolované stránky dostanou nějakou hodnotu page rank
- Page rank je možné vyjádřit také maticí
	- Důležité vědět: matice je stochastická, neredukovatelná a aperiodická.
	- stochastická - suma řádku se rovná 1

### HITS
>A page is a good hub if it points to good authorities, and a page is a good authority if it is pointed to by good hubs.
- v podstatě 2 ranky (hub rank, authority rank)