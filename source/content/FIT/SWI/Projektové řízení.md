Ultimátní cíl: dodat produkt / službu
- včas, v požadované kvalitě, s očekávaným chováním a v dohodnuté ceně

Projekťák je osoba zodpovědná za řízení projektu, sleduje a řídí:
1) náklady / cenu - kolik můžeme utratit
2) čas, termíny / milníky - co do nich má být hotovo
3) rozsah / pracnost - pozor na bobtnání požadavků, řízení změn
4) kvalitu - jestli výstupy splňují požadovaná kritéria
5) rizika - události, které mohou během projektu nastat a ovlivnit projekt
6) přínosy

Musím stanovit, jakým způsobem bude software dodáván (většinou je to po částech)
	- těm způsobům se říká SDLC (= software development lifecycle)
	- jsou věci, které se musí provádět vždy 
		- analýza a sběr požadavků - Use Case, User Story ([[Analýza požadavků]], [[Modelování obchodních procesů]])
		- návrh architektury a detailní design ([[Návrh architektury aplikace]])
		- implementace a testování ([[Testování aplikace]])
		- dodání a údržba ([[Podpora a údržba systému]])
	- metody se liší v tom, jak se ty kroky provedou, kdy se provedou, v jakých návaznostech atd.

**Vodopád** - sekvence pracovních postupů (hezky jeden po druhým) 
- nelze se vracet zpět (pouze o jeden krok)
- vhodné pro projekty, kdy jsou všechny požadavky známé od začátku a jsou neměnné - už není moc šance měnit v průběhu vodopádu
- nevhodné pro moderní vývoj

**Iterativní model** - sekvence malých vodopádů, mezi sekvencemi se dodává zákazníkovi
- průběžné dodávky - mám zpětnou vazbu od zákazníka
	- lze změnit směr podle nových požadavků a potřeb zákazníka
- nevýhoda, projekt se tím celkem prodlužuje

**[[Agilní přístup]]** - iterativní model, kdy délka iterace je zkrácena na minimum
- rychlá zpětná vazba a rychlá reakce na změny požadavků
***
### Příprava projektu
- vyhodnotím proveditelnost a přínosnost - jestli má projekt v aktuální podobě vůbec cenu
- porozumění obsahu práce a tvorba "high-level" projektového plánu
- alokace zdrojů, tvorba týmu
- výběr přístupu
- stanovení výstupů projektu

**Výstup projektu** není jenom aplikace, ale i dokumentace (analytická dokumentace, User Manual, příručka pro administrátory), také služby (příprava testovacího a monitorovacího prostředí, migrace dat, školení, podpora)

**Odhad pracnosti projektu** - můj úsudek bude ze začátku špatný, je lepší počítat (podle požadavků, případů užití, počtů obrazovek, podle tabulek, služeb atd.) - tady se hodně opírám o analytickou část projektu
- schraňuj si historická data, budou se hodně hodit do budoucna
- do pracnosti je potřeba zahrnout také svoje řízení, analytiky, testery, rizika apod. (ne jenom programování)
- odhad pracnosti, který mi dá programátor je většinou příliš optimistický
- místo konkrétních odhadů spíš odhadní hranice
- nedávej úmyslně nižší odhad - slevu pro zákazníka je věcí obchodníka
- pozor - mám problém s tlakem na úzké hranice (snažím se být co "nejpřesnější")
- pro odhady existují metody: Karnerova metoda a COCOMO (podle počtu řádků)
	- pokud metody detailně neznám, tak nepoužívat, jsou potřeba i historická data a menší odchylka může udělat velký rozdíl

**Alokace zdrojů** - sepsání rolí a přidělení lidí k rolím

**Projektový plán** - rozpad projektu na jednotlivé úkoly
- termíny dodávek a akceptací
- je potřeba součinnost zákazníka
- můžu použít Ganttův diagram
- pojem: kritická cesta - nejdelší cesta v projektu, určuje minimální délku projektu
	- může jich být více

### Provádění projektu
- zde projekťák komunikuje se zákazníkem a informuje ho o průběhu projektu
	- řešení nedorozumění, nejasností, upřesnění 
	- schůze, zápisy a jejich potvrzování
	- projednávání rizik a poznávání potřeb zákazníka
- komunikace v týmu
	- přidělování úkolů, kontrola jejich plnění
	- vykazování odpracovaných hodin
	- odstraňování překážek (blockery mezi programátory)
- vyhodnocování projektového plánu
	- hlídat rozsahy, sledovat termíny
	- upravovat plán podle aktuálního stavu
	- řešit hrozby a příležitosti
- evidence rizik - zapsání rizik, které mohou nastat (např. zákazník včas nepřipraví produkční prostředí)
	- přidám jim pravděpodobnost, že se stanou a závažnost
	- plán pro jejich odstranění, předcházení, zmírnění
- měření projektu
	- spotřebovaný čas - man daye
	- náklady, termíny, kvalita

### Uzavření projektu
- evidence historických dat
	- trvání projektu
	- pracnost, rozsah, cena 
	- problémy a jejich příčiny
	- vyhodnocování původních odhadů
- odhadování příštích projektů