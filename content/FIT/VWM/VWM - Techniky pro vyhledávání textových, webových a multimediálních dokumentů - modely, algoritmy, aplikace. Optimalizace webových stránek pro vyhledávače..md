### Základní pojmy
##### Rozdíl search X browse
- search - hledám konkrétní věc
- browse - nehledám nic konkrétního, spíš daný prostor procházím
##### WWW (World Wide Web)
- graf webových stránek + další zdroje hostované na webových serverech
- komunikace přes protokol HTTP
- internetový prostor je zaměřen na prezentaci lidem (GUI) a informace se špatně čtou strojům
##### [[URL, URI, URN, IRI|URL]] = uniform resource locator
- odkaz na zdroj (webová stránka, multimédium atd.)
- je to out-link z webové stránky

Meta search engine - vyhledávač, který agreguje výsledky z dalších vyhledávačů
##### Typické části vyhledávání
1) crawling - stahování obsahu (webové stránky)
2) indexing - procesování obsahu do formy vhodné pro vyhledávání
	1) díky indexování už procházím malinkou část obsahu a ne celé univerzum ve full podobě
3) searching - získávání relevantního obsahu pomocí dotazu
##### Tradiční vyhledávače
1) full-text indexování a analýza linků
	1) dotazy jsou klíčová slova či full-text
2) keyword query
	1) dotaz obsahující pouze pár klíčových slov
3) full-text query
	1) celý full-text zparsovaný do keyword query
##### Multimediální vyhledávače
1) content-based queries (navíc ke keyword query)
2) [[Získávání informací z multimédií]]
	1) o tom, jak vyhledávat multimédia, jak se anotují apod.
		1) pak jsou tam i metody ohledně podobnostního vyhledávání v multimédiích, to není ve scope této otázky
##### Obecné způsoby získávání informací (dokumentů)
1) dotaz (query)
	- uživatel je schopný specifikovat svůj záměr
	- je to jednorázový proces vyhledání
	- keyword-based a content-based (uploadne soubor, nahraje z URL, nakreslí sketch)
2) browsing
	- uživatel není (dobře) schopen specifikovat svůj záměr
	- skákání přes explicitní linky nebo přes "doporučené" linky - např. s podobným obsahem
3) filtering
	- filtrování obsahu pro mě (personalizace, podle mého chování a mé zpětné vazby...)
	- je to pokračující proces vyhledávání
	- explicitní filtrování (na základě nějakého statického požadavku - dotaz, předplatné, RSS channel)
	- implicitní filtrování (doporučování, na základě preferencí, navštívených stránek, historie apod.)

Je potřeba umět získávat samotné informace z textu (textové dokumenty, stránky apod.)
### [[Získávání informací z dokumentů a stránek]]
- zmiňuje obecné predispozice a lore k tomu a pak se zaměřuje na konkrétní metody
- [[Boolský vyhledávací model]]
- [[Vektorový vyhledávací model]]
### Optimalizace webových stránek pro vyhledávače
- nejdřív je potřeba porozumět tomu, jak fungují: [[Page Rank a HITS]]
- [[Search engine ranking a SEO]]

