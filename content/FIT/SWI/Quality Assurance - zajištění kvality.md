= sada aktivit, kterými zajistím kvalitu projektu, nabízí systematický přístup k zajištění kvality

Proč chci kvalitu?
- zákazník je spokojený, má naplněná očekávání
- je to investice do budoucnosti
	- cena za opravy chyb s progresem projektu stoupají exponenciálně
		- oprava na úrovni návrhu či implementace je jinak náročná než pak když je projekt už hotový a musí se udržovat
		- čím dříve chybu najdu, tím levnější je ji opravit
	- snížené náklady za opravy v rámci záruky
- moje dobré jméno

Prvně se musí kontrolovat naplánování procesů - co chci, aby aplikace splňovala?
1) sledují se externí charakteristiky - podpora uživatele aplikace
	- korektnost - neobsahuje chyby, splňuje požadavky zákazníka
	- použitelnost - uživatel se snadno naučí pracovat s naším SW
	- efektivita - SW efektivně využívá HW zdroje
	- spolehlivost - nízká četnost výpadků
	- integrita - zabránění neoprávněného přístupu
	- robustnost - reakce na nevalidní vstupy a stresové situace
2) chci také podporovat vývoj (interní charakteristiky)
	- udržovatelnost - snadný vývoj, opravy chyb
	- testovatelnost - snadné psaní testů
	- znovupoužitelnost - možnost využití v jiných částech systému
	- čitelnost - snadné pochopení zdrojových kódů
	- srozumitelnost - snadné pochopení na obecnější architektonické úrovni

=> v podstatě dodržování všeho, co jsme doteď dělali

Co se pro zajištění kvality dá dělat?
- školení - analytiků, programátorů, testerů, uživatelů
- formální inspekce/revize projektu
- prototypování
- testování
- párové programování

Měřit kvalitu můžeme také pomocí statické analýzy kódu.
- ta odhaluje typické chybové vzory
- cyklomatická složitost (počet nezávislých cest funkcí)
- nedodržování jmenných konvencí