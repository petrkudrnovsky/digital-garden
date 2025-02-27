- umožňuje přenášet data (tabulky, sloupce, řádky) mezi Postgresovými databázemi
- replikace probíhá na ==úrovni SQL příkazů== 
- logická replikace podporuje ==pouze DML operace==, ne DDL operace
- typicky se používá na:
	- replikaci jenom části dat
	- migraci mezi verzemi Postgresu
	- integrace dat z více zdrojů na jedno místo
***
### Jak to funguje?
![[Pasted image 20241202143659.png]]
Publisher databáze - tam se manipuluje s datama
Subscription databáze - tam se ukládají a replikuji data z Publisher databáze
- jedná se o producer-consumer problém

Publication - objekt, který definuje data, které chci, aby byly replikované
- je ho možné mít pro konkrétní tabulku nebo pro celé schéma apod.
	- real-life use case: mám spešl schéma, které chci replikovat a to si pak replikuju a už to nenastavuju pro konkrétní tabulky, ale jenom je přidám do tohoto schématu
- můžu si vybrat, jaké DML příkazy se budou replikovat (defaultně jsou všechny)

Subscription - replikuje data a objekty z definovaného Publication
- Subscriptionů k jednomu Publication může být prakticky neomezené množství
- nepodporuje DDL příkazy, takže si tabulky, které chci replikovat musím vyrobit sám (jsou podporované pouze operace DML)
- tohle může vytvořit pouze uživatel v roli `SUPERUSER`

Initial Snapshot - udělá se sám

[[Write Ahead Logs (WAL)]] - zachytávají změny, které se pak propisují na Subscription tabulkách
- logická replikace je používá pro přenos změn na úrovni SQL příkazů

Replication Slot - drží WAL logy, které jsou potřeba pro replikaci (aby např. nebyly odstraněny [[Write Ahead Logs (WAL)#Událost checkpoint|checkpointem]]) + drží si informaci o tom, jaké změny už byly provedeny (tím, že si udržuje [[Write Ahead Logs (WAL)#LSN|LSN pozici]])
- Producent má replication slot pro každého konzumenta

Row filters - můžeme replikovat jenom nějaké řádky (na základě nějaké hodnoty)
Column list - mohu replikovat jenom nějakou podmnožinu sloupců

Logická replikace je jednosměrná
- změny, které si udělám na konzumentovi, tak se nebudou propagovat do producera
***
By default - logická replikace je asynchronní, ale je možné ji mít synchronizovanou:
### Synchronizovaná logická replikace
![[Pasted image 20241202152658.png]]
- jde to po šipkách od klienta (člověk, appka) doprava a pak celou cestu doleva
- publisher nevrátí odpověď dokud nemá potvrzení do subscribera, že je ready to commit a že commitnul
- synchronizovaná přináší ale větší zátěž a latenci (používáme jenom v případech, kdy je konzistence prioritou na např. kritických datech)

