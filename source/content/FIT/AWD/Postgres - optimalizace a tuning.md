> [!success] Tipy pro lepší výkon
> - používej `ANALYZE` a `VACUUM ANALYZE` příkazy často
> - RAIDy > RAM > CPU
> - lepší mít více disků než jeden velký -> pro používání [[#Tablespaces]]
> - zvlášť úložný prostor pro [[Write Ahead Logs (WAL)|Transaction logy]], oddělené od databáze
> - pro disky používat SCSI
> - více CPU pro více databázových připojení (paralelizace)
> - pro přidávání hodně dat najednou:
> 	- použít příkaz `COPY` místo `INSERT`
> 	- odstranit [[#Indexy]] během přidávání
> 	- zvyš `maintenance_work_mem` 
> 	- `fsync = false` (pak ale zase zapnout)
> 	- potom spusť `ANALYZE`
> - používej dedikovaný server pro databázi
> - data a [[Postgres - záloha a obnovení|zálohy]] měj na různých discích
> - používej journal-based systém

> [!todo] Postup optimalizace dotazu
> 1. najdi problematické SQL dotazy (jsou časté nebo provádí se dlouho)
> 2. podívej se na jejich prováděcí plán
> 3. pochop ho a rozhodni, jak může být vylepšený
> 4. aplikuj vylepšení
> 5. zkontroluj, jestli to zabralo
> 6. typicky se to musí dělat postupně

Zlepšování výkonu databáze. Výkon můžeme zlepšovat více různými způsoby.
### Optimalizace dotazů
##### Prováděcí plány a statistiky
- použití `EXPLAIN` a `EXPLAIN ANALYZE` pro analýzu prováděcích plánů vykonání dotazů
	- na základě patternů v prováděcích plánech můžeme najít části, které by šly optimalizovat
- příkaz `EXPLAIN` - zobrazí prováděcí plán dotazu, ale samotný dotaz neprovede + odhadne cenu dotazu
	- databázový stroj si pro každý dotaz udělá více prováděcích plánů, přiřadí jim cenu (cost) a pak vybere a provede ten nejlevnější (opírá se o statistiky používání)
	- `EXPLAIN ANALYZE` - dotaz provede a zobrazí prováděcí plán se skutečnou cenou a skutečnými časy
	- snižování ceny dotazu se říká **cost-based optimalizace** 
- příkaz `ANALYZE`
	- sbírá statistiky o tabulkách a indexech
	- optimalizátor dotazů pak tyto statistiky používá k tomu, aby rozhodnul o nejlepším prováděcím plánu dotazu
	- často se používá ihned po příkazu `VACUUM` [[#Operace `VACUUM`]] pro získání nových, aktualizovaných, statistik
	- je vhodné ho použít i po velkých zásazích do databáze - hromadný import či mazání
		- pak má databázový stroj neaktuální statistiky a nemusí nejlépe optimalizovat prováděcí plány
	- statistiky se ukládají do tabulek `pg_statistic`
- rozložení složitého dotazu na menší části (sníží složitost a zátěž optimalizátoru)
##### Indexy
- zrychlují vyhledávání v tabulkách a snižují počet řádků, které je potřeba skenovat
- nejčastější index je: B-Tree, pak jsou speciální na full-textové vyhledávání, geometrická data atd.
- indexy ale zase zabírají místo navíc a zpomalují DML operace (vkládání, mazání záznamů atd.), protože se indexy musí udržovat aktuální
	- takže je potřeba také identifikovat nepoužívané indexy a dávat je pryč
##### Materializované pohledy
- pohled je pojmenovaný select, ale může to být fakt složitý select, který se pořád "překládá" a optimalizuje
	- můžu si udělat *materializovaný pohled*, který jednou optimalizuje ten select a uloží výsledná data fyzicky na disk a pak už jen vrací tato data místo znovu-provádění toho dotazu
		- což je při opakovaných volání mnohem rychlejší
	- problém je, že se taková data neaktualizují a nereagují na změny v databázi - ale jsou use-cases, kdy mi to nevadí
	- jde napsat job, který bude refreshovat tyhle materializovaný pohledy (třeba přes noc, kdy není takový provoz)
***
### Optimalizace úložného místa 
##### Operace `VACUUM`
- při transakcích si Postgres při každé změně řádku uloží novou i starou verzi toho řádku, protože neví, jestli na konci řeknu commit nebo rollback - a podle toho pak zneplatní jednu z těchto verzí (ale zneplatněná pořád zabírá místo)
	- standardní `VACUUM`
		- uvolní místa, kde byly expirovaná data (takže ty datové bloky jsou volné pro zaplnění, ale opticky se velikost databáze moc nezměnší)
	- `VACUUM FULL`
		- kompletně restrukturalizuje tabulky a fyzicky zmenší místo na disku
		- je pomalejší a blokující
	- proces **autovacuum** - je automatický v Postgresu a stará se o většinu údržby, ostatní `VACUUM` použiju, když potřebuju víc (např. mám velké tabulky s častými změnami nebo provedu masivní aktualizace nebo mazání)
##### I/O operace
- pomocí cache - zvýšení cache = zvýšení výkonu
	- `work_mem` - pro třídění a hashovací operace
	- `maintenance_work_mem` - pro údržbové operace (`VACUUM`, `CREATE INDEX`)
	- `shared_buffers` - sdílená paměť mezi procesy Postgresu
##### Tablespaces
- tablespace = prostor na disku, na kterém fyzicky existují tabulky
- mohu s tím definovat kde budou uložené logy, archivovaná/historická data, uživatelská data apod. - podle typu disku a taky podle toho, jak rychle potřebuji dostupná konkrétní data (dá se optimalizovat) - rychlá data na SSD, pomalejší na HDD například
##### [[Write Ahead Logs (WAL)]]
- mohu nastavovat velikost paměti, interval mezi checkpointy atd.