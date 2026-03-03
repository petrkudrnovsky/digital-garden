- Roadmap: https://roadmap.sh/data-analyst
- Kniha: https://www.statlearning.com/
- Videa: https://www.youtube.com/@misraturp 

Proces datové analýzy
1. definování základní otázky (společně s business stakeholders)
	- díky těmto otázkám můžu zúžit rozměr dat, se kterými pracuji
2. sbírání dat
3. zkoumání dat
	- pročíst si additional information files (většinou jsou přidané k datům), popis 
	- v Jupyter Notebooku si je vizualizovat a zobrazit ([[01 Python]])
	- sledovat, jestli jsou data čistá (různé hodnoty pro stejnou věc (např. 1-2, 1to2, 1/2 atd.)), jestli neobsahují nějaké duplicitní hodnoty, chybějící hodnoty apod.
	- psát si otázky, čemu nerozumím a pak se ptát konkrétních lidí
	- Misra Turp ohledně Data Exploration zde [YT video](https://www.youtube.com/watch?v=OY4eQrekQvs&ab_channel=M%C4%B1sraTurp) 
4. čištění dat
	- následuje po zkoumání dat (mám poznamenané problémy, otázky a další věci)
		- duplikace, překlepy (Spell checker), špatné datové typy
		- chybějící hodnoty 
			- pokud je to opravdu málo - dá se buď smazat celý řádek a nebo chybějící sloupec vyplnit nejčastější hodnotou (abych neovlivnil data tolik a zároveň si nechal ostatní důležité a vyplněné hodnoty sloupců konkrétního řádku)
			- nebo vyplnit průměrnou hodnotou
		- přípony/předpony, které jsou zavádějící, nepotřebné atd. (Find and replace)
		- různá velikost textu (uppercase, lowercase)
		- mezery navíc, další trailing bílé znaky (Trim)
		- různé formáty datových údajů (měsíc, den, rok atd.)
		- čísla jako stringy, problémy s mínusem u záporných hodnot
		- dělení a slučování sloupců (adresa -> ulice, čp, město, nebo firstName, lastName -> name)
		- uspořádání dat do tabulkového formátu, prohození řádků a sloupců
		- vyřešení odlehlých hodnot (outliers)
			- mohou to být i špatně zadané hodnoty (např. s nulou navíc) 
			- buď je odstraním nebo je mohu zvýšít/snížit na 25% a 75% percentil
				- např. maximum je 55 a 75% je 8, tak tam to vypadá hodně na outlier, který nesedí s 75% percentilem (a pak ani se střední hodnotou)
5. transformace
6. analyzování dat
7. vizualizace a prezentace výsledků a zjištění stakeholderům

Nejčastější typy datové analýzy:
1. regresní analýza
2. faktorová analýza (= redukce dimenze) - pomáhá zjištění, jaké proměnné ovlivňují jaké a zvládne kondenzovat jednotlivé proměnné do "super" proměnných a díky tomu se s nimi lépe pracuje
3. kohortní analýza - kohorty jsou skupiny pozorovaných jednotek, které mají společné vlastnosti. Pomocí kohortní analýzy sleduji změny těchto kohort v čase a porovnávám je mezi sebou
4. cluster analysis - rozdělení jednotlivých dat do clusterů (podle různých metrik) a sledování rozdělení dat do jednotlivých kategorií
5. analýza časových řad - sledování jedné metriky v průběhu času - hodí se pro identifikaci časových změn (sezónnost, trendy apod.) a předpovídání budoucnosti 