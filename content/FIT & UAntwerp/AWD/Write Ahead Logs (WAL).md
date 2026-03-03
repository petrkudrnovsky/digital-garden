 write-ahead log (WAL) = journal = transaction log
- v průběhu transakce se generují změnové vektory (change vectors)
- říkají, která transakce změnila jaký data (**id transakce, id řádku, stará hodnota, nová hodnota**) + commit, rollback, [[#Událost checkpoint|checkpoint]]
- po commitu se obsah journalu uloží na disk
	- díky tomu se může databáze po recovery ([[Postgres - záloha a obnovení]]) dostat do správného stavu
	- výsledky transakcí se totiž nemohou rovnou zapisovat na perzistentní úložiště (protože by se přetížily I/O operace)
		- takže se to rychle nahraje přes journaly a pak se to postupně ukládá/nahrává
			- a kdyby to kleklo, tak se je postup uložený v journalu a může se to podle toho obnovit
### Událost checkpoint
- změny se totiž nejdřív ukládají právě do WAL logů
- a checkpoint je událost, kdy se nesesynchronizované změny z WAL logů uloží fyzicky na disk
	- pak si systém uloží aktuální informace o systému do WAL logů
	- a zahodí staré WAL logy, protože už nejsou potřeba
- tato událost se spouští automaticky cca každých 5 minut (jde nastavit)
- checkpointy snižují dobu obnovení, protože snižují počet WAL logů, které je potřeba přehrát
### LSN
- *= log sequence number*
- je to pointer do WAL logů
- je důležitý pro zachování konzistence v rámci [[Postgres - logická replikace]]
	- používá se v replikačních slotech