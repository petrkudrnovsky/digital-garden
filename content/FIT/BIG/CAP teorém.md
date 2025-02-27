Vymyslel ho Eric Brewer v roce 2000. 

> V distribuovaných systémech nelze zajistit současně:
> 1. konzistenci (C) - všechny uzly mají stejná data v reálném čase
> 2. dostupnost (A) - každý požadavek je zpracován a vrátí se výsledek (nemusí být vždy aktuální)
> 3. toleranci vůči výpadkům (P) - tedy systém funguje při výpadku části sítě
> **NoSQL databáze v jeden moment splňují maximálně 2 ze tří garancí**

- jedná se o vlastnosti, které bych chtěl, aby ideální distribuovaný a replikovaný databázový stroj splňoval
- aktuálně to není úplně binární, jednotlivé garance se mohou měnit či prolínat, ale v zásadě to pořád platí - alespoň jednu garanci musíme ošidit

- C + A = not partition tolerant (RDBMs)
	- V případě výpadku části (nebo sítě) je to problém.
	- Než aby systém vracel nekonzistentní výsledky, radši neodpoví vůbec.
- C + P = not always available (MongoDB)
	- Pokud systém nedokáže vrátit aktuální data tak vrátí chybu.
- A + P = not consistent (Cassandra)
	- Neexistuje záruka, že čtená data jsou aktuální.
	- Tolerantní k výpadku dílčí služby

Existují systémy, které se rozdělí do více segmentů a komponent a každá splňuje jiné garance v rámci CAP teorému - záleží na využití, na typech dotazů atd.

**V reálu nejde zajistit 100 % stabilitu sítě, takže musím počítat s tím, že se mi to jednou rozpadne - tedy volím mezi C (odmítnout požadavek) a nebo A (vrátit potenciálně neaktuální data)**

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfdmTRh4FdR4V9fULTZPnxQN2mZRk9zIf14m4QedftrBKZgBFxry6mQQC_taQYo15gmNac2JtXRN5KzOLFRT5qp3ezgsV9dbYluyeETD7MuG4xEIeM-fYzbSKpZGgtK38EZHk2NAfTdGpJG6724XZ6Yas4?key=yGg6q_DMLJu5fx9VlDS_rQ)

***
Prakticky:
- [[Apache Cassandra#CAP teorém|Apache Cassendra]], Dynamo, CouchDB preferuje AP (dostupnost a toleranci)
- [[MongoDB]], Hbase a [[Redis]] podporuje CP (konzistence a tolerance)
- klasické SQL relační databáze mají CA (tedy rozdělení sítě je problém)