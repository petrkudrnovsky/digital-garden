- MPM = multi processing modul
- kontroluje vytváření potomků (procesů a vláken)
- můžu použít POUZE JEDEN
	- výběr záleží na OS, který používám
# Pro Linux
## prefork
- pro správu procesů, obsluhu požadavků zajišťují procesy
- počet požadavků = počet procesů, které je obsluhují
- root proces rozděluje požadavky na procesy (pokud je nějaký volný)
- pokud mám moc požadavků
	- můžu nastartovat další proces
	- to jsou další systémové nároky - omezím si to, aby mi nedošla paměť
- stabilní a jednoduchý na debug
- zabírá více paměti (každý proces má vlastní paměť) a není tak výkonný při více procesech najednou
![[Pasted image 20250111181855.png]]
- `Min/MaxSpareServers` - mám nějaké volné, abych neplácal zdroje, ale aby byly nějaké hned ready, až přijde požadavek (startování procesu trvá nenulový čas)
## worker
- pro správu vláken (je starší, aktuální je [[#event]]), obsluhu požadavků zajišťují vlákna procesů
	- kombinuje tedy procesy a vlákna
	- pooluje procesy, které pak mají více vláken, které pak obsluhují uživatele
- je potřeba mechanismus zamykání paměti
- je to výkonnější
- je potřeba používat thread-safe knihovny, má složitější debug (pád vlákna ovlivní další vlákna v procesu)
- hodí se na statický obsah - tady sdílená paměť nevadí
![[Pasted image 20250111183504.png]]
#### Problém Slow Loris
- navážu spojení a jako klient posílám data hrozně pomalu (můžu být útočník)
	- server se nudí, protože nemá (skoro) nic na zpracování
- ale rychle se vyplýtvá počet otevřených procesů
	- a server pak odmítá požadavky
- `mod_reqtimeout`
	- nastavím si nějakou svoji min. rychlost přijímání dat
	- pokud je to moc dlouhé atd. - tak ukončím spojení
	- různé timeouty pro hlavičky a jiné pro tělo
	- nějaká hodně špatná spojení (neútočící) to eliminuje :// - takže ne uplně ideální řešení
## event
- nabízí asynchronní zpracovávání, lepší podporu `Keep-Alive` spojení (u moderních webových aplikací)
- vyhradíme skupinu vláken, aby jenom přijímaly požadavky 
	- paralelně se tahají data od klientů a AŽ je celý požadavek hotový, tak se pošle do obslužného vlákna, které pak požadavek zpracuje
	- jedno vlákno paralelně přijímá požadavky
		- problém, že někdo zdržuje a natahuje mi už nevadí (vyřešen problém [[#Slow Loris]])
![[Pasted image 20250111183519.png]]