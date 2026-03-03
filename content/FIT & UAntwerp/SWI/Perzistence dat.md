Větší objemy dat je potřeba ukládat zvlášť.
### Memory storage
- při restartu je paměť zahozena, tedy je to ideální pro cache, krátkodobou [[Session]] apod.
- velikost paměti není velká
- př. cache, SQlite (in-memory)
### Souborový systém
- data uložena v jednom a více souborech
- problém s konkurečním přístupem (zamykání souborů)
- cacheuje se, protože zápis a čtení z disku je náročná operace
- [[Session]], dlouhodobá cache, SQlite atd.
### Databáze
- relační vs. nerelační (př. NoSQL)
- implementuje efektivní ukládání dat
- lépe řeší zámky a přístup k datům
- př. relačních: MySQL, MSSQL, Oracle, PostgreSQL...
- př. nerelačních: Redis, Neo4J, CouchDB, MongoDB...

Existuje více způsobů, jak nakládat s daty z databáze.
##### Table data gateway
- jedna instance pro všechno
- jedna instance řídí všechny řádky v tabulce (tedy všechny instance dané entity), zapouzdřuje všechny SQL dotazy
![[Pasted image 20230523225916.png]]
##### Row data gateway
- jedna instance řídí jeden řádek v tabulce (tedy jednu instanci třídy)
- modifikace - lze nemít instanci pro komunikaci s DB vedle, ale všechno mít implementované v rámci třídy jako statické metody (ale je to nedoporučené - statické metody se špatně testují)
- metody pro načítání, ukládání a mazání v databázi jsou v konkrétní instanci
- existuje speciální třída "jen" pro vyhledávání instancí
![[Pasted image 20230523225835.png]]
##### Active record
- obdoba Row data gateway, akorát přidává business logiku a tím porušuje zásady [[Návrh architektury aplikace#Třívrstvá aplikace|třívrstvé architektury]]
- objekt reprezentuje řádek v tabulce
	- součástí objektu jsou mimo jiné i metody pro práci s databází
	- tedy se objekt umí sám uložit, načíst konkrétní instanci atd.
- logika ukládání a business část je společná
![[Pasted image 20230523225851.png]]
##### Data mapper
- komplexní mapování objektového světa do relačního
- instance není vázána na konkrétní tabulku
- mapování je uloženo buď v konfiguračních souborech či ve formě anotací
- mám zvlášť entitu držící data a zvlášť entitu, která ji mapuje do DB
- odděluje logiku a data
- samotný mapper obsahuje logiku pro ukládání dat a tím pádem objekt obsahuje jen svá data a business logiku
![[Pasted image 20230523225904.png]]
##### Work of Unit
- nějaký manager si drží změny, které chci v databázi udělat a pak je provede jako frontu za sebou (`$manager->flush()`)
- za mě je to dobré, že si můžu vybrat, kdy se budou provádět změny do databáze a můžu tím urychlit chod aplikace, že to rozdělím do větších bloků, které se budou provádět najednou