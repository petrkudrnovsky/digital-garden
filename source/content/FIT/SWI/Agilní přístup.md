### [Agilní manifest](https://agilemanifesto.org/iso/cs/manifesto.html)
- jednotlivci a interakce > procesy a nástroje
- fungující SW > dokumentace
- spolupráce se zákazníkem > vyjednávání o smlouvě
- reagování na změny > dodržování plánu

### Principy
Největší priorita je vyhovět zákazníkovi častým dodáváním hodnotného softwaru. Změny v požadavcích v pozdějších fázích vývoje nejsou takový problém.

Lidé z businessu a vývoje musí na projektu spolupracovat denně a po celou dobu projektu. 

Vytváří se prostředí pro jednotlivce, podporujeme je a důvěřujeme jim, že odvedou dobrou práci. 

Nejúčinnější a nejefektivnější způsob sdělování informací je **osobní konverzace**.

Měřítko pokroku je fungující software.

Tým se pravidelně zamýšlí nad tím, jak se stát efektivnější a následně tak koriguje svoje chování a zvyklosti.

### Výhody
- rychlejší dodávky - iterativní vývoj - iterace je zkrácena na minimální dobu
- vhodné i pro menší projekty
- rychlá zpětná vazba
- rychlá reakce na změnu požadavků
- klíčový je spolupracující zákazník
	- zde se to může seknout, zákazník nemusí mít tolik času
	- rozsah není předem vymezený smlouvou, to se nějakým nelíbí

### Problémy
- u velkých týmů
- zákazník není schopen denně spolupracovat a zástupce zákazníka nemá odpovídající pravomoci
- důvěra, že i bez smlouvy se dodá očekávaný systém
- slovo "agilní" dost často funguje jako buzzword je to jenom marketing

### SCRUM
Pojmy:
- Scrum Team (do 10 členů)
	- Scrum Master - kontroluje dodržování praktik metodiky
		- odstraňuje překážky
	- Product Owner - vybrán Scrum Masterem, zákazníkem a managementem
		- odpovídá za chod projektu
		- řídí priority a upravuje položky Product Backlogu
	- Developers - hlavním cílem je realizace funkčností ve Sprint Backlogu
		- odhadují pracnost položek v Product Backlogu
- Product Backlog - seznam všech požadavků, které se od aplikace očekávají
	- mají priority a logické závislosti
- Sprint Backlog - definice cíle sprintu (proč?)
	- seznam požadavků/úkolů, které se realizují během jednoho sprintu
	- plán práce pro dodávku
- Sprint - iterace, jejíž výstupem je nová verze produktu pro zákazníka
	- cca 1 měsíc nebo méně
	- požadavky během sprintu jsou fixní a nelze je měnit
	- Sprint planning - plánovací schůzka na začátku
		- výběr a upřesnění položek z Product do Sprint Backlogu
	- Sprint review - zhodnocení výstupů sprintu
		- prezentován zúčastněným stranám
	- Sprint retrospective - hledání způsobu, jak zlepšit efektivitu spolupráce
		- blockery, vyhodnocování problémů
- Scrum - iterace uvnitř sprintu, trvá jeden den
	- Scrum meeting na 15min na začátku
	- Otázky pro člena: Co jsi udělal včera? Co uděláš dnes? Máš blockery?

Fáze:
1) předehra 
	1) plánování - sestavení Product Backlogu a odhadování pracnosti
	2) [[Návrh architektury aplikace]] - vysoký stupeň abstrakce
2) hra
	1) iterativně jedeme Sprinty (cca 3-8 Sprintů)
3) dohra
	1) [[Integrace aplikace]]
	2) [[Testování aplikace]] a akceptační testy
	3) Dokumentace
