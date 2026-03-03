Vymyslel ho Eric Brewer v roce 2000. 

> V distribuovaných systémech nelze zajistit současně:
> 1. konzistenci (C) - všechny uzly mají stejná data v reálném čase (všechny read a write operace musí proběhnout atomicky (na všech serverech najednou))
> 2. dostupnost (A) - každý požadavek je zpracován a vrátí se výsledek (nemusí být vždy aktuální) - pokud daný node funguje
> 3. toleranci vůči výpadkům (P) - tedy systém funguje při výpadku části sítě - tj. systém je schopen ztratit libovolné množství zpráv 
> **NoSQL databáze v jeden moment splňují maximálně 2 ze tří garancí**

- jedná se o vlastnosti, které bych chtěl, aby ideální distribuovaný a replikovaný databázový stroj splňoval
- aktuálně to není úplně binární, jednotlivé garance se mohou měnit či prolínat, ale v zásadě to pořád platí - alespoň jednu garanci musíme ošidit

- C + A = not partition tolerant (RDBMs)
	- V případě výpadku části (nebo sítě) je to problém.
		- pokud se zjistí výpadek sítě, tak musí všechny nody přestat přijímat požadavky od uživatelů (aby nedošlo k porušení konzistence)
	- Než aby systém vracel nekonzistentní výsledky, radši neodpoví vůbec.
	- Dá se dosáhnout [[ACID]] vlastností
- C + P = not always available (MongoDB)
	- Pokud systém nedokáže vrátit aktuální data tak vrátí chybu.
- A + P = not consistent (Cassandra)
	- Neexistuje záruka, že čtená data jsou aktuální.
	- Tolerantní k výpadku dílčí služby
	- nový koncept [[BASE model|BASE]] vlastností

Existují systémy, které se rozdělí do více segmentů a komponent a každá splňuje jiné garance v rámci CAP teorému - záleží na využití, na typech dotazů atd.

**V reálu nejde zajistit 100 % stabilitu sítě, takže musím počítat s tím, že se mi to jednou rozpadne - tedy volím mezi C (odmítnout požadavek) a nebo A (vrátit potenciálně neaktuální data)**
- v reálu se většinou trochu relaxuje konzistence (a za tuto cenu dostanu hodně dostupnosti)
	- CAP teorém je o tom, že nemohu mít všechno najednou na 100 %

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfdmTRh4FdR4V9fULTZPnxQN2mZRk9zIf14m4QedftrBKZgBFxry6mQQC_taQYo15gmNac2JtXRN5KzOLFRT5qp3ezgsV9dbYluyeETD7MuG4xEIeM-fYzbSKpZGgtK38EZHk2NAfTdGpJG6724XZ6Yas4?key=yGg6q_DMLJu5fx9VlDS_rQ)
### O konzistenci
- konzistence je o tom, aby v databázovém systému nebyly žádné rozpory (operace musí být "atomické")
	- jde o to, že po každém zápisu, všichni čtenáři musí číst stejná data
	- změny po zápise se musí ihned propagovat na další nody
- rozdělujeme ji většinou na silnou konzistenci a eventuální konzistenci
	- eventuální konzistence je většinou v reálu dostačující (1min starý článek na portálu většinou nevadí)
- možné problémy:
	- write-write konflikt na peer-to-peer architektuře
		- pesimistické řešení - write locks
		- optimistické řešení - don't care now, pomocí version-stampů, vector-clocks se konflikt vyřeší pak
	- read-write konflikt (nekonzistentní čtení)
		- po zapsání dat chvíli trvá než se data propíšou do replik (tzv. inconsistency window)
	- silná konzistence se řeší pomocí "quorums" (kvóty)
		- nastavím si $W$ a $R$ 
			- write quorum: pokud se chce zapisovat, tak musí $W>N/2$ , tj. počet nodů účastnící se zápisu musí být více než polovina replikačního faktoru (tj. nadpoloviční většina replik se musí účastnit zápisu)
			- read quorum: pokud se chce číst, tak $R>N-W$ 
				- pokud jsou vrácené verze jiné, tak se vezme ta novější
				- neboli $R+W>N$ -> tady je zajištěno, že se bude číst z alespoň jednoho aktuálního nodu
		- například pro $N=3$:
			- $W =3$, tj. všechny 3 repliky musí být aktualizované
			- $R=1$, tj. může se číst z jakékoliv z nich
		- dobrý trade-off je $W=2$ a $R=2$ 
			- tady už nemůžeme číst z jakékoliv repliky, protože při zápisu nejsou aktualizované všechny, tj. bychom mohli mít smůlu a číst z té neaktualizované - proto se musí min. 2 shodnout
				- pokud obě mají různé verze, bere se ta novější
		- pokud kvórum není splněno, požadavek se nemůže přijmout

***
Prakticky:
- [[Apache Cassandra#CAP teorém|Apache Cassandra]], Dynamo, CouchDB preferuje AP (dostupnost a toleranci)
- [[MongoDB]], Hbase a [[Redis]] podporuje CP (konzistence a tolerance)
- klasické SQL relační databáze mají CA (tedy rozdělení sítě je problém)