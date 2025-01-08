### Klasifikace testů
Dělení podle rozsahu:
1) jednotkové
2) integrační
3) systémové testy

Dělení podle způsobu provádění:
1) statické testy - není potřeba, aby program byl spuštěný
2) dynamické testy - vyžadují spuštěný kód

Jsou white box testy - píšeme se znalostí zdrojového kódu, testujeme konkrétní implementaci. Umožňují nám tak více identifikovat případné problémy, ale zase jsou citlivější na změnu implementace.

Black box testy. Tam se neřeší konkrétní zdrojový kód, program je pro test černá skříňka. Ověřuje se chování a reakce na rozhraní.

Gray box testy. Známe, jaký je použitý algoritmus, ale neznáme konkrétní implementaci.

Testy se spouští buď automaticky či manuálně.

Testy se také liší podle svého zaměření ([[Analýza požadavků|FURPS]])

### Proč testovat?
- můžeme tím měřit kvalitu kódu
- pokud píšeme testy už při vývoji, tak nás to nutí psát metody a třídy tak, aby se daly testovat (= lepší návrh)
- co nejdříve odhalit chyby a problémy
- testem také popíšu, jaké je očekávané chování produktu

Výsledkem testů mohu prokázat JEN existenci chyb, NEMŮŽU prokázat, že SW chyby neobsahuje.

### Samotné testování
- nejdřív se vytváří testovací plán - co se testuje, jak, s jakými nástroji, kdo testuje a kdy se bude testovat
- Test Case
	- popisuje postup provedení testu, vstupní a výstupní data a jaké jsou výsledky
- Test Suite
	- kolekce Test Cases

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

