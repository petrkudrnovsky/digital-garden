= REmote DIctionary Server

Typ databáze: klíč-hodnota
- jak název napovídá, data jsou uložena jako klíč a hodnota
- klíče jsou indexované pro rychlý přístup
- jsou velmi jednoduché na implementaci, jsou dobře horizontálně škálovatelné ([[Big Data#Škálovatelnost]])

Klíče i hodnoty mohou být různé typy jako text, číslo, seznam, set, hash mapa, JSON atd.
Používá se jako mezipaměť pro RDBMS, jako cache pro různé aplikace, ukládání session dat nebo výsledků dotazů nějaké databáze pro rychlejší odezvu

- neexistuje dotazovací jazyk, jenom možnost se dotazovat na klíč
- největší výhoda je rychlost, open-source řešení a vysoká dostupnost
- data jsou primárně ukládána v paměti RAM (primární paměť)
	- dle konfigurace pak může data synchronizovat i na disk nebo ukládat do souborů, ze kterých pak jde daná data rekonstruovat
### Redis Sentinel
- služba a nástroj pro správu Redis, který monitoruje dostupnost uzlů, přebírá služby při selhání (např. repliku povýší na hlavní a překonfiguruje zbytek) a poskytuje konfiguraci (klienti se připojují a ptají se na adresu aktuálního master uzlu)
	- zajišťuje tím vysokou dostupnost databáze
### Redis Cluster
- služba na automatické dělení a distribuci dat
- pro horizontální škálování (sharding)
- klíče jsou na základě hashovací funkce rozdělovány do slotů (a ty jsou na jednotlivých uzlech)
- zajišťuje dostupnost i při výpadku části uzlů díky replikaci dat mezi uzly
### Architektura
Může být
- single-node (na jednom pc)
- master-slave
Replikace
- master-slave architektura
- v případě failu mastera Redis Sentinel povýší nějakého slave
### Pipelining
- řetězení příkazů a poslání všech najednou, což rapidně zvýší výkon a sníží latenci (až 10x rychlejší)
- ![[Pasted image 20241209222638.png|300]]
### Perzistence (2 módy)
- můžeme perzistenci zakázat (pak data ztratím, jakmile vypnu stroj)
- RDB (Redis Database)
	- v pevně nastavených intervalech dělá zálohu (snapshot) databáze
	- v případě výpadku přijdu o data od poslední zálohy
	- dobré pro zálohování
- AOF (Append-Only File)
	- každá operace se zaznamená do logu 
	- vhodné pro minimalizaci ztráty dat
### Prakticky
- `SET` a `GET` pro zápis a čtení (+ `INCR` a `DECR`) a `DEL` pro smazání klíče
- `FLUSHDB` - odstranění všech klíčů
- `SWAPDB` - umí prohazovat databáze na které jsou klienti připojení
- data se většinou rozdělují do kategorií podle prefixů klíče (dvojtečková notace)
	- `jidlo:ovoce:banan`
- klíčům jde nastavit TTL pomocí `EXPIRE`
- GUIčko: RedisInsight
- v Redisu jde skriptovat pomocí jazyka Lua (např. aplikační logika v Redisu)
### Moduly
Redis má spoustu modulů, které rozšiřují jeho funkcionalitu a prakticky z něho dělají plnohodnotnou databázi
- RedisSearch - fulltext vyhledávání
- RedisGraph - práce s grafovými daty
- RedisTimeSeries - ukládání a analýza časových řad
- atd.
### Redis Pub/Sub (Publisher a Subscriber pattern)
- publikace a odběr zpráv, čímž Redis umožňuje asynchronní komunikaci mezi aplikacemi napojenými na Redis databázi
- např. pro notifikace mezi aplikacemi, zpracování událostí nebo streamování dat
- datový typ `MESSAGE BROKER`
### [[CAP teorém]]
- spadá do kategorie CP, může se stát částečně nedostupným
- klade velký důraz na konzistenci dat a proto jsou transakce strikně nedělitelné
	- ale transakce nesplňují plný [[ACID]], např. ne Isolation
		- může se stát, že transakce pracují najednou na stejných datech
		- splňuje pořádně pouze atomicitu

