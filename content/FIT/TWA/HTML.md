Full name: *HyperText Markup Language*
Všechno důležité najdu zde na [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/HTML) 

- není to programovací jazyk, pouze značkovací jazyk, který udává strukturu dokumentu a propojuje stránky pomocí odkazů
- povinná deklarace: `<!DOCTYPE html>` (povinná od verze 4)
	- [[Problém hlavičky HTML v kontextu HTTP protokolu]]
- podobný [[XML]] - existuje [[XHTML]]
- meta data: autor - člověk, který je zodpovědný za tu stránku
	- `<meta name="author" content="Jan Novák">`

- jednotlivé úrovně nadpisů nepřeskakuji - mají sémantický význam - kdyžtak si je jinak nastylovat akorát (stejně bych nejspíš používal [CSS Reset](https://meyerweb.com/eric/tools/css/reset/))
- `<div> a <span>` - slouží jenom ke strukturování a nemají žádný sémantický význam
- `<ol> a <ul>` - chci mít (ne)seřazená data, nedávám je podle toho, jak chci, aby ty seznamy vypadaly (mají jenom sémantický seznam) - tedy se zamyslet, jestli ty data mají být seřazené nebo je to jenom seznam

- HTML má fakt řešit jenom sémantickou strukturu (VŮBEC neřeším vzhled), tedy ani nevolím `<ol>` či `<ul>` podle toho, jak to vypadá, ale podle toho, jakou to má mít sémantickou strukturu
	- veškerý vzhled by mělo řešit CSS
- standard HTML: https://html.spec.whatwg.org/

- tagy dělíme na **blokové** a **řádkové** 
### Zajímavé tagy
`<address>` obalovač pro kontaktní informace
`<sub> <sup>` = subscript a superscript
`<main>` hlavní část dokumentu
`<article>` celistivá část dokumentu ("menší" než section)
`<section>` část, např. blok komentářů u novinky
`<header>` hlavička
`<nav>` navigace
`<aside>` vedlejší informace, nesouvisí s hlavním obsahem
`<footer>` patička
`<blockquote>` blokový "citát"
`<abbr>` poskytnutí vysvětlení, nápovědy k nějaké zkratce v textu
`<dl>, <dt>` definiční seznamy, sémanticky se hodí pro vypisování údajů o něčem
`<picture>` pro přidání více obrázků (více zdrojů), které se budou měnit na základě velikosti obrazovky, rozlišení a možnostech zařízení
- je to vhodné zvlášť pro implementování responzivních obrázků
- např. různé obrázky pro landscape a portrait mode
- mohu optimalizovat načítání stránky pro mobily/počítače načítáním různě velkých obrázků
### HTML5
- revoluce v HTML, přidáno hodně sémantických tagů
- starší tagy dostaly nový význam (`<b>` nebo `<i>`) 
	- `<b>` - zvýraznění textu, který vyžaduje pozornost, ale bez zvláštního důrazu (např. na klíčová slova, názvy produktů v recenzi apod.) - na přitáhnutí pozornosti
	- `<i>` - zvýraznění textu (= odlišení od normálního), např. technický termín, jiný jazyk, myšlenka apod.
	- nerozlišovat tyto tagy kvůli tomu, že jeden bude *bold* a druhý *kurzívou*, to pak řeší [[CSS]], rozlišení se děje opravdu jen sémanticky
##### Schema.org
- v rámci HTML5 byly přidány další atributy, díky kterým můžeme lépe definovat obsah na webových stránkách a používat k tomu ontologie jako [schema.org](https://schema.org/docs/gs.html) 
	- díky tomu budou vyhledávače a např. AI lépe rozumět kontextu a obsahu na mé stránce a budou ho moci relevantněji využít a zobrazit lépe
- říká se tomu ==Microdata== a jde o definování lidí, předmětů, akcí a dalších entit na webu
	- také je to o specifikování času pomocí `<time datetime="">`
	- různých informací o stavech např. zboží (na skladě, vyprodáno...)
	- doplňování kontextu k vizuálním zobrazením (např. počet hvězdiček zobrazený animací/obrázkem) nebo k odkazům