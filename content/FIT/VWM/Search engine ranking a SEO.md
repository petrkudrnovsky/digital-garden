- mám nějaké "ohodnocení" stránek (z Boola, Vectoru, LSI, PageRanku)
- SEO je metoda, jak tvořit webové stránky, aby "rankovaly" co nejvýše
	- adaptace stránky na vyhledávací algoritmus, který se aktuálně používá
	- spíš je to už taková kuchařka, co všecho "platí"

### Rankování obecně
- mám spoustu informací (o stránkách - z PageRanku, o uživateli - preference atd.)
- ==One step ranking== 
	- mám v atributech uložený různé ranky stránky (PageRank, vektor, LSI)
		- podle podoby obsahu, podoby rozložení (layout), PageRanku (- ten nezáleží na dotazu, je obecný)
	- mám agregační funkci, která tyto ranky projede a pro každý vyplivne jeho finální hodnotu
		- musí být **monotónní**
		- klasické agregační funkce mohou být Min, Max a Avg
	- tedy mám různé rankingy stránek a po agregační funkci chci, aby je zagregovala, např. "zprůměrovala"
	- ==Top-k operátor==
		- řeší se jenom prvních k stránek
			- nemohu to počítat pro všechny, bylo by to moc náročný
		- Fagin a Threshold algoritmy (fun fact, oba vymyslel týpek Fagin, ale ten druhý už po sobě nemohl pojmenovat) jak najít top-k výsledků na základě několik atributů (různé ranky, PageRank, layout atd.)

### Faginův algoritmus
- implementuje one step ranking a [[Získávání informací z multimédií#Pozdní fúze (late fusion)|Top-k operátor]]
- čtu tak dlouho, že pro všech k stránek(objektů) mám všech m ranků, které potřebuji k agregování (tedy všechny)
	- až zjistím všechny hodnoty ranků pro prvních k stránek
		- tak musím doplnit celou tabulku (tedy pro ostatní skočím po pointeru k jejich hodnotám)
- optimalizovaný na to, aby minimalizoval náhodný vstupy
- nevýhoda je, že může zajet hodně daleko a tím pádem má velmi velký buffer (klidně i půlka DB)

### Threshold
- drží si určitý práh (= threshold) 
- theshold se mi postupně snižuje a 
- přístup je sekvenční narozdíl od paralelního Fagina
- sekvenčně přečtu a rovnou si doplním zbývající hodnoty přes pointery 
	- kandidát je 0.6, ale můj treshold je 0.9 (nemůžu dostat nic lepšího než 0.9)
		- ale mezi 0.9 a 0.6 je rozdíl, takže tam může být lepší výsledek, jedu dál
		- takhle jedu do té doby, než mi treshold nespadne pod ten "horší výsledek" a tím pádem
- nevýhoda
	- RandomAccessem přistupuje častěji (zjišťuje si další hodnoty ihned) - a ten je o něco pomalejší
- výhoda
	- zjišťuje fakt jenom top-k stránek a nenaroste do tak velkých hodnot
- je univerzálnější

Když vybírám, jaký použít, tak to záleží na hodnotě k, m a taky, jak drahý je RandomAccess vs. sekvenční přístup
I když je one-step ranking optimalizovaný Faginem a Threshold, tak jsou furt drahý

==Multi-step ranking==
- nejdříve chci "agresivní filtr" - např. podle contentu (hledám "Botanická zahrada" a vyfiltruju mnohem více stránek než co by mi dal samotný PageRank)
	- omezím si množinu a pak už si to jenom "šoupu" podle ostatních ranků

## SEO
>The content of web page that is good to human is not enough, it should be also “good” to the machine.

- matoucí název, neoptimalizuji engine, ale optimalizuji stránky PRO engine
- používají se technologie webového designu, ale v souvislosti s tím, jak funguje vyhledávací engine
- chci zlepšit dojem ze stránky, ale pořád zachovat směr a organiku stránky 

**Etické SEO**
- dlouhodobé přidávání a optimalizování organického obsahu -> dlouhodobý SEO efekt
**Neetické SEO**
- agresivní manipulace pro okamžitý SEO efekt
	- nedávat klíčová slova o zahradních sekačkách pro stránku o nábytku
	- engine to identifikuje a klasifikuje mě jako spam
- search engines rankují jednotlivé stránky, takže je potřeba mít optimalizovanou každou stránku mé webovky
- chci optimalizovat pro engine a pro uživatele zároveň
	- ta myšlenka zatím původně je tak, že optimalizuji jen pro uživatele a search engine se na to přizpůsobí, ale v praxi to takhle moc není (velká konkurence na webu být první ve výsledcích)


### Jak to dělat?
- optimalizovat URL stránky (čitelné URL) a URL textově relevantní k obsahu (a čím kratší, tím lepší)
- název stránky (jen do 64 znaků, více Google nezobrazí) - primární ranking faktor
- meta-tagy - už nejsou moc relevantní, hodně se zneužívali
	- *description* - může se použít pro specifikování, o čem daná stránka je 
	- *keywords* - už nejsou moc relevantní kvůli zneužívání
- zvýrazňovače textu (jak člověk, tak engine je bere jako významnější)
	- např. strong -> tohle vnímá uživatel i engine
- optimalizování obrázků - pokud se na stránce nachází obrázky, jsou to body nahoru
	- engine nemůže "číst" obrázky, je potřeba dobře vyplnit alt tagy
		- keyword vyplň relevantní pouze k danému obrázku, ne k celé stránce
		- once again, pokud budu používat alt ke keyword spammingu, tak mi to dá penalizaci
- jaký je content, struktura linků uvnitř
	- pokud mám vnitřní strukturu linků, tak se spideři mohou rychle pohybovat po všech relevantních stránkách
	- taky záleží na tom, kde jsou linky umístěné
		- nahoře > dole
		- v bloku textu > osamocené
- 2 skupiny klíčových slov - chci dobré namixování těchto skupin
	- generické 
	- více relevantní

- mixovat správné keywords (abych se trefil do dotazů zákazníků)
	- obecnější a specifičtější klíčová slova
	- na hlavní stránku spíše ta obecnější (pro vyšší traffic) a na substránky spíše specifičtější
	- udržovat správnou hustotu klíčových slov
		- když nějaké opakuji víckrát, tak v rámci [[Vektorový vyhledávací model|vektorového modelu]] se vektor pro daný term více prodlouží, pokud budu používat více různých a jenom jednou, tak zase vektor poroste více do stran
- stejně nejdůležitější je ==organický obsah==
	- celé SEO by mělo být augmentace, aby ten organický web lépe sedl na search engine
	- obsah by se měl psát pro uživale, ale stejně se píše i "pro search enginy" - ty se totiž při rozhodování chovají podobně jako uživatel
	- je důležité udržovat obsah aktuální
		- search engine si kontroluje míru updatů stránky a hodně staré stránky už třeba ani neindexuje
			- samozřejmě je tam spoustu jiných parametrů - když je stará, ale pokud na ní lidé pořád chodí, tak ji search engine nechá nahoře
	- potřeba GDPR, Cookies, mít privacy policy stránku
- dále engine penalizuje stránky, které mají duplikovaný obsah (a neřeší, která to "opsala" od které)
- je potřeba optimalizovat i další soubory (PDFs, MS Office atd.) - např. u PDFka se ujistit, že se jedná o machine-readable text a ne pouze o bitmapu
- udělat si rešerši stránek, které na mě mohou ukazovat
	- linkování ze sociálních médií (zapojení uživatelů)
	- chci linky z důvěryhodných a prověřených stránek
	- linky z .gov a .edu jsou více ceněný

#### Crawler
- krátký program, který instruuje tzv. "Spiders" (roboti, boti, agenti) o tom, jak a jaké stránky mají procházet
- každá stránka by měla mít soubor `robots.txt`, který takovým botům říká, které části stránky mají dovoleno procházet
	- existuje etické crawlování, kdy to crawleři dodržují, ale také to spousta porušuje (je to jen na bázi dobrovolnosti) - **Robot exclusion protocol**
		- například e-mailoví sběrači, malware, spamboti atd.

#### Sitemap
- soubor (html, php, xml), který pomáhá robotům se orientovat na stránce
- měl by následovat hierarchii stránky
- může být ve formátu XML

#### Robots.txt
- říká robotům, jaké stránky můžou indexovat a crawlovat a jaké ne
- v rootu webového serveru
- také s ním mohu kontrolovat crawlování multimédií (tím pádem šetřit přenosem na serveru) a copyright informacemi

#### .htaccess
- nastavení funkcionality Apache serveru
- použití: ochrana některých složek heslem
	- redirecting
	- banování botů z určitých zdrojů

#### Neetické SEO
- lze to "nafejkovat" např. linkovými farmami atd.
- ale většinou je to dosti krátkodobé 
	- search engine mě najde a dá na blacklist