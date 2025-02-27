Provádíme UCD - tedy návrh zaměřený na uživatele. Jak něco navrhnout tak, aby se to pak uživatelům dobře používalo. UCD není moc metoda, která nám říká co a jak přesně dělat, je to spíš takový postoj, taková filosofie. Existuje spoustu postupů a metod pro spoustu různých situací.

Má mnoho metod pro:
1) prozkoumávání, získávání dat ([[Metody a techniky práce s uživateli]])
2) porozumění a interpretace dat ([[Metody vyhodnocování při návrhu artefaktu]])
3) projekce porozumění do samotného návrhu
4) spolupráci mezi uživatelem a návrhářem ([[Metody a techniky práce s uživateli]])
	- hodně v [[Agilní přístup|agilním světě]]

Má 3 hlavní fáze:
1) formativní fáze
	- analýza kontextu ([[Kontextová analýza]])
	- průzkum uživatelů (schopnosti, preference, potřeby, aktivity) ([[Průzkum uživatelů]])
		- [[Metody a techniky práce s uživateli]]
	- analýza domény a prostředí (obor, fyzické prostředí, sociální prostředí) ([[Doménová analýza]])
	- vyhodnocení ([[Metody vyhodnocování při návrhu artefaktu]])
		- kdo jsou uživatelé? jaké mají schopnosti, preference?
		- jak probíhá proces, který podporujeme svým artefaktem
		- zkrátka vyhodnocujeme, co jsme se naučili (z [[Doménová analýza|doménové analýzy]], [[Kontextová analýza|kontextové analýzy]])
2) návrh
	- postupné zjemňování a testování prototypů
	- [[Informační architektura v UCD]]
	- logická struktura ([[Logická architektura v UCD]])
	- [[Vizuální návrh v UCD]] a [[Vzorce chování uživatelů]]
	- vyhodnocení
		- rozumíme dobře tomu, jak a proč se artefakt používá?
		- je náš konkrétní nápad užitečný? (lepší než ostatní jiné varianty)
		- je v pohodě použitelnost (naše hlavní zaměření) - už máme totiž prototyp
			- [[Testování použitelnosti v UCD]]
		- když existují problémy, co je jejich příčinou
3) sumativní fáze
	- závěrečné testování použitelnosti
	- testování správnosti návrhu ([[Testování použitelnosti v UCD]])
	- pozorování funkce artefaktu ve skutečném nasazení [[Metody a techniky práce s uživateli#3) Terénní průzkum]]
	- vyhodnocení
		- ještě jednou se zeptáme, jestli je použitelnost v pořádku?
		- byly naše předpoklady správné? je artefakt sociálně přijatelný?
		- co jsme se dozvěděli z procesu návrhu a nasazení (a můžeme popř. použít příště)
Po každé fázi přichází [[Metody vyhodnocování při návrhu artefaktu|vyhodnocení]] - je to analýza výsledku dané fáze, ptáme se na otázky a snažíme se na ně upřímně odpovědět. Hodně se zaměřujeme na hledání příčin existujících problémů.

### Návrhář
Jako **návrhář** mám vytvořit **artefakt** (= něco uměle vytvořeného za nějakým účelem, např. aplikace) pro **uživatele** tak, že je to použitelné a přináší to uspokojení potřeb.

Návrhář je mostem mezi technologiemi a společností.

##### Důvěra
Vztah mezi návrhářem a zákazníkem stojí na důvěře. Tu vytváříme pomocí:
1) dobré vůle - uživatel ví, v čí prospěch se informace, kterou nám dává, použije
2) integrity - víme, že se dané slovo dodrží a víme, že se říká celá pravda (např. o monitorování, záznamech apod)
3) schopností dostát svým závazkům
***
V UCD se zaměřuji hlavně na použitelnost a užitečnost aplikace, jak definuje Jacob Nielsen:
![[Pasted image 20230613220404.png]]
- nezáleží jen na hezkém UI - je potřeba promyslet procesy, databázi, pojmy, funkce atd.
- artefakt by měl být **prakticky přijatelný** - dělá to opravdu to, co to má dělat
- artefakt by měl být **sociálně přijatelný** - aby se lidem, kteří s ním pracují, pracovalo dobře, aby nikdo nebyl diskriminován 

### Koloběh návrhu
![[Pasted image 20230613223036.png]]
- nejjednodušší práce pro návrháře je navrhovat artefakty pro lidi jim podobné, kdy návrháři mají určité znalosti o uživatelích, mají zkušenosti
	- využijí introspekci - prostě návrh udělají tak, jak by vyhovoval jim samotným
		- introspekce = pohled od sebe, pohled dovnitř
- problém je, když se uživatelé artefaktu obecně liší od návrháře
	- např. v dovednostech a předchozích zkušenostech
	- v paměťových a kognitivních schopnostech
	- v cílech a preferencích
- je potřeba použít metody k [[Průzkum uživatelů|poznání uživatelů]] a funkce artefaktu ve společnosti ([[Doménová analýza]])
- v tomto případě je introspekce zavádějící a profesionální návrhář by ji neměl používat

### Rozdíl UCD X [[Týmový projekt|Softwarové inženýrství]] 
UCD
- hledá porozumění cílům uživatele a podporuje ho
- dospívá k požadavkům analýzou kontextu a úloh
- vyhodnocuje užití artefaktu
- zaměřené na koncového uživatele

Softwarové inženýrství
- řeší životní cyklus aplikace a spravuje jej
- práci začíná sběrem požadavků
- vyhodnocuje splnění požadavků
- zaměřené na zákazníka