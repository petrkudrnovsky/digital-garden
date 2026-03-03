## Proč testovat?
- můžeme tím měřit kvalitu kódu, správnost fungování SW
- pokud píšeme testy už při vývoji, tak nás to nutí psát metody a třídy tak, aby se daly testovat (= lepší návrh)
	- snadnější refactoring
- co nejdříve odhalit chyby a problémy (čím dříve odhalená chyba, tím levnější je její oprava)
- testem také popíšu, jaké je očekávané chování produktu - testy slouží jako dokumentace

Výsledkem testů mohu prokázat JEN existenci chyb, NEMŮŽU prokázat, že SW chyby neobsahuje. Nemáme absolutní jistotu.
## Klasifikace testů
Dělení podle rozsahu:
1) jednotkové (unit) - rychlé a levné
2) integrační
3) end-to-end testy - pomalé a drahé

Dělení podle způsobu provádění:
4) statické testy - není potřeba, aby program byl spuštěný
5) dynamické testy - vyžadují spuštěný kód

Dělení podle aspektu, který testujeme
1) liší se podle svého zaměření ([[Analýza požadavků|FURPS]])
	1) funkční testy - testují se "funkční" požadavky (F...)
	2) nefunkční testy - testují se "nefunkční" požadavky (performace, security apod.) (...URPS)

Dělení podle způsobu spouštění
1) manuální 
2) automatické - znovu použitelné, konzistentní
#### Unit testy
- převážně [[#White, black, grey testy|whitebox]] testy, testují metodu/třídu/modul nezávisle na ostatních
- low-level, izolované, zaměřené na malou část systému
	- žádná databáze, souborový systém, síťová komunikace atd.
- jsou rychlé a levné
- používají se test doubles (náhrada skutečného objektu za jiný - stejné rozhraní, jiná implementace)
	- stub - vrátí očekávanou odpověď na očekávané volání
	- mock - stejně jako stub, ale umožňuje ověřit, že volání bylo provedeno tak, jak bylo očekáváno
#### Integrační testy
- testují, jestli souhra více komponent systému funguje dobře
	- testuje se rozhraní mezi systémy
- testují integraci aplikace s databází, API, souborovým systémem atd.
- externí službu je možné mockovat (existuje i Mock API, které si jde nastavit)
- pro databázi jsou 2 přístupy
	- dočasná DB jen pro test (in-memory/Docker)
	- dedikovaná testovací DB
- dva přístupy (dobré je je kombinovat)
	- narrow integration test - komunikuje se s jednou komponentou (nebo jenom její částí)
		- lépe se mockuje, rozsahem je podobně jako unit test
	- broad integration test - vyžaduje více komponent/modulů najednou
#### End-to-end testy
- simulace postupu uživatele aplikací
- testují celou aplikaci jako celek (včetně integrací a dalších služeb)
- většinou jsou [[#White, black, grey testy|blackbox]]
- vyžadují hodně údržby, jsou pomalejší, snadno se rozbijí
- testují skutečný [[Modelování případů užití (Use Cases)|use-case]] 
- spouští se v headless browseru (nastavím klikání, texty vložené do formuláře atd.)
#### White, black, grey testy
Jsou white box testy - píšeme se znalostí zdrojového kódu, testujeme konkrétní implementaci. Umožňují nám tak více identifikovat případné problémy, ale zase jsou citlivější na změnu implementace - jsou křehké.

Black box testy. Tam se neřeší konkrétní zdrojový kód, program je pro test černá skříňka. Ověřuje se chování a reakce na rozhraní. Jsou robustnější.

Grey box testy. Známe, jaký je použitý algoritmus, ale neznáme konkrétní implementaci.
### Samotné testování
- v souvislosti s [[Continuous Integration (CI) a Continuous Delivery (CD)]]
- ![[Pasted image 20250406145035.png]]
- průběh jednoho testu
	- ARRANGE - příprava objektů/prostředí pro testování
	- ACT - v rámci testu se zavolá metoda/funkce/služba, která je testována
	- ASSERT - kontrola, jestli se výsledek rovná očekávanému výstupu
***
**Boundary testing** - vysoká pravděpodobnost nalezení chyby
- black-box testování edge casů a hraničních případů
**Regresní testy** - opětovné testování, že nové funkce "nerozbily" stávající
- tyto testy se hodí automatizovat
**Smoke testy** - rychlý test sloužící k ověření, že je aplikace připravena (nasazena, spuštěna)
- ověřuje jen hlavní funkce - přihlášení a dostupnost pro další testování
### Metriky testů, které sledujeme
Jaké je pokrytí kódu testy (100% neznamená, že jsou pokryté úplně všechny průchody)
Počet nalezených chyb
Počet provedených Test Cases
Kvalita testování - čím vyšší kvalita, tím méně nalezených chyb v produkci
### Vlastnosti testů
1) power test - vysoká pravděpodobnost odhalení chyby (důsledný, komplexní test...)
2) maintainable - snadno udržitelný i při změnách implementace
3) credible - odpovídá očekávanému chování uživatele
4) repeatable/reusable - lze snadno použít znovu (a na jiných místech)
5) value - odhalí chyby důležité pro uživatele
6) easy to evaluate - snadno vyhodnotitelné

Pro testování je většinou jiné prostředí - testovací prostředí. Pokud je na testování moc dat (např. z produkce), tak se mohou provádět tzv. **řezy dat** 
- zmenší požadavky na kapacitu disků
- je potřeba zajistit reprezentativní vzorek dat a jejich konzistenci
- problém pro výkonnostní testy - ty jsou k ničemu na malých vzorcích dat

Dále (hlavně kvůli GDPR) by se měly data anonymizovat a odstranit citlivé informace - ale měly by se zachovat charakteristické vlastnosti
- délka řetězců, formáty dat (např. tel. číslo)
- závislosti mezi tabulkami
- vnitřní logika (rodné číslo)
- odlišný obsah v závislosti na jiném údaji

