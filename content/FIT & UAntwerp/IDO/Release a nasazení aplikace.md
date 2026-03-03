- pomocí [[Continuous Integration (CI) a Continuous Delivery (CD)]] jsme sestavili a otestovali aplikaci
- před nasazením do produkce se většinou nasazuje na testovacího prostředí pro zákazníka, aby si ji mohl vyzkoušet a otestovat
- DevOps řeší problémy, které vznikaly mezi předávání informací mezi vývojáři a správci serverů (kteří aplikaci nasadí a budou provozovat)
- nechceš nasazovat manuálně
	- může se stát nějaká lidská chyba
	- proces nasazení je mnohdy pomalejší
## Release
- = uvolnění nové verze aplikace pro předání zákazníkovi
- vždy musí být specifikovaná přesná verze aplikace [[Balíčkovací systémy a sémantické verzování]] a zdrojové kódy (verze knihoven, konfigurační soubory atd.) pro tento release
	- společně s tagem
- release aplikace - většinou rychlé "nahrazení"
- release databáze - je složitější, je potřeba dát pozor na poškození/ztrátu dat - skripty jdou většinou spustit jednou (na DDL změny nejde mít rollback)
	- důležité mít zálohu
	- skripty jsou
		- opakovatelné - při každém nasazení
		- neopakovatelné
			- úplné - vytvoření nové databáze
			- inkrementální - vyžaduje specifickou konkrétní předchozí verzi DB
	- dá se řešit např. verzovanými migracemi (kde jsou změny i "antizměny")
#### Feature flag
- možnost do release dát i funkčnosti, které budou dostupné jenom po "zapnutí" přepínačem (např. ještě nejsou úplně hotové)
- zpřístupnění pouze pro vybranou množinu uživatelů
#### Prostředí aplikace a jeho správa
- většinou je potřeba více prostředí
	- testovat na produkčních datech nikdy není dobrý nápad
- DEV = vývojové, většinou na lokálním pc
- TEST = testovací
	- pro automatické testy v rámci CI
	- pro manuální testování ze strany zákazníka
- UAT = akceptační prostředí
	- co nejpodobnější produkci, školení zaměstanců
- PROD 
- zdrojové kódy jsou nezávislé na prostředí
- konfigurace
	1) nezávislá na prostředí
	2) specifická pro jednotlivá prostředí (je v rámci [[Continuous Integration (CI) a Continuous Delivery (CD)#Nástroje|nástrojů pro sestavení a nasazení]])
- izolace prostředí - neměly by se mezi sebou ovlivňovat
- nasazování by mělo být autorizováno někým zodpovědným
	- chráněné větve, kam nemohou hned pushovat všichni
	- manuální potvrzení
#### Pravidla nasazování
- jeden artefakt je použit ve všech prostředích
- z pohledu struktury by si jednotlivá prostředí měla být co nejpodobnější
- postupné nasazování DEV, TEST, UAT a pak PROD
- staré soubory nemazat, aby byla možnost se vrátit k předešlé verzi
- minimalizovat manuální zásahy
- nikdy nedělat změny přímo na produkčním prostředí