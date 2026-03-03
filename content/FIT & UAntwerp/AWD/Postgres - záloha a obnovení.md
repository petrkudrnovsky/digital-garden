### Logické zálohy
> [!question]- Co je logická záloha?
> Logická záloha vytváří soubor SQL příkazů, které mohou obnovit strukturu a data databáze.
- vytváří se pomocí `pg_dump` (vznikne SQL soubor) a obnovují pomocí `pg_restore`
- jsou pouze logické a neobsahují dostatek informací pro kompletní obnovu
### Fyzické zálohy
- *file-system based* pro kompletní obnovu
> Máme 2 hlavní způsoby zálohování. Liší se v tom, jestli databázový server zrovna běží nebo ne. Pokud je shozenej, tak není problém, protože do toho nikdo nesahá a všechno je synchronizované (tzv. **cold backup**). Náročnější je pak **hot backup**, který se provádí za běhu databáze.
##### Online (hot) file-system based backup
- třetí způsob zálohování
- databáze se zálohuje za provozu a při připojených klientech
- probíhá PITR (= point in time recovery) - [PostgreSQL: Documentation: 17: 25.3. Continuous Archiving and Point-in-Time Recovery (PITR)](https://www.postgresql.org/docs/current/continuous-archiving.html)
	- samotná databáze se zálohuje po nějakých časových úsecích (například po týdnu udělám plnou zálohu databáze (**hot backup**))
		- jsou nástroje, které to umí (většinou si databáze zmrazí kontext, aby byl start i konec zálohy konzistentní) a během backupu a za provozu se další operace ukládají do [[Write Ahead Logs (WAL)]] - ten nástroj na zálohu do nich může taky koukat a archivovat si je, aby pak po backupu mohl uvést databázi do aktuálního stavu
	- a změny od poslední zálohy nám ukládají [[Write Ahead Logs (WAL)]]
	- v případě potřeby obnovy, tak si mohu obnovit databázi z jakého časového bodu chci do jakého časového bodu chci

Utilita `pg_basebackup` 
- možnost inkrementálních záloh (které nezabírají tolik místa)
	- v tento případ je potřeba "předhodit" předchozí zálohu
- umí právě hot-backup za provozu

Nejde obnovit jenom 1 adresář, musím obnovit celou databázi