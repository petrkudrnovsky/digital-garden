- jednotlivé dokumenty jsou kódované v JSONu, BSONu (binární verze JSONu) a nebo ve formátu XML
	- dokumenty jsou tedy většinou hierarchické stromové struktury
	- jsou organizovány do různých kolekcí
- dokumenty vedle samotných dat obsahují i metadata
	- a mohou mít různou strukturu, nejsou omezený nějakým pevně daným schématem
- využívají se i u webových aplikací, protože dokumenty se mapoují na objekty (ideální na OOP vývoj)
- dá se říct, že jsou to rozšířené [[Key-Value databases]], kde "value" část je možné prozkoumat na úrovni databáze => je to strom 
	- v Key-value stores je hodnota black-box pro danou databázi
- je možné vytvářet indexy nad poli dokumentů pro rychlejší vyhledávání
- každá dokumentová databáze má svůj dotazovací jazyk a nebo API
### Výhody
- dobře se mapují na objekty a tím pádem se dobře integrují s moderními aplikacemi
- jsou flexibilní - není potřeba striktně definovat a dodržovat strukturu dat
- vysoký výkon - dokumenty se ukládají jako jedna jednotka (neexistují joiny)
- škálovatelné - dobrá podpora shardingu
### Nevýhody
- nepodporují joiny, je složitější se dotazovat na data napříč kolekcemi
- velké dokumenty mohou být neefektivní, když se dotazuji pouze na část (tak se musí načíst celý)
- mohou se objevovat duplicitní a nadbytečná data
- není zaručená integrita dat - není zajištěné to, že jsou všechna data konzistentní
	- jak je všechno uložené u sebe
### Use cases
- logování eventů, CMS systémy, blogy, webová analytika, e-commerce aplikace
	- všechno, kde se dají využít strukturované dokumenty s podobným schématem
- kdy spíše nepoužívat:
	- když se schéma/design dokumentů často mění
	- když se často aktualizuje více dokumentů najednou
### Konkrétní databáze
- [[MongoDB]]
- CouchDB, DynamoDB
- Base X

