> Jedná se o programovací paradigma (= programming model) a implementaci pro zpracování velkých objemů dat v distribuovaných systémech. Jeho síla je v rozdělování složitých úloh do menších, paralelně zpracovatelných částí. 

[YT Computerphile](https://www.youtube.com/watch?v=cvhKoniK5Uo&ab_channel=Computerphile) 

Vyvinul ho Google v roce 2004 a implementován v nástroji [[Hadoop]]. Udělal to hlavně kvůli tomu, aby mohl efektivně počítat [[Page Rank a HITS|Page Rank]] pro všechny zalistované stránky rychle a efektivně (už nebylo možné mít centrální index, přesuny dat po síti byly drahé, servery často vypadávaly apod.).

Pro využití frameworku je potřeba implementovat pouze Map a Reduce fáze, vše ostatní zařídí framework (distribuce dat, řazení úkolů, řešení chyb jednotlivých nodů atd.)
- také je potřeba implementovat Input Reader, Partition function, Compare function atd. - ale ten už existuje pro všechny základní datové typy (implementovat musím, pokud chci zpracovávat nějaké pokročilé nebo vlastní soubory)
# Proces
- data jsou rozdělená na různých serverech v clusteru
- založen na principu Rozděl a panuj (Divide and conquer)
### Map fáze
- na každém uzlu se provede mapovací funkce ("přivedeme výpočet k datům")
- vstupem jsou data ve formátu klíč-hodnota (před mapovací fází proběhne tzv. Input Split, který podle nějakého algoritmu zparsuje a rozdělí obsah daného splitu do key-value formátu)
- výstupem mapovací funkce je vypočtená sada dvojic klíč-hodnota
	- většinou je to nějaký klíč a k němu vypočtená hodnota
	- klíče mohou být duplicitní
- vypočtené dvojic jsou uložené do jednotlivých regionů (v paměti workera nebo ve filesystému)
	- rozdělení do regionů dělá tzv. Partition function (např. hash klíče + modulo)
- *chci provést stejnou úpravu na datech, který mám rozfrkaný po celým clusteru - tak místo toho, abych ty data dal na jedno místo a provedl úpravu na jednom místě, tak tu úpravu provedu na X místech najednou*
### Mezifáze
- data ve formátu klíč-hodnota se pak třídí a seskupují podle klíče (Shuffle and sort)
- existuje tu tzv. Partition function
	- podle klíče rozhodne, do jakého regionu (pro reduce fázi) daný klíč padne
	- každý reduce node může vzít víc regionů najednou (kde na ně pak aplikuje reduce fázi)
- a pak se dané regiony distribuují na společný reduce node (pohromadě podle jednoho klíče)
	- ALE! až potom, co skončí mapovací fáze na všech uzlech (ty reportují master uzlu, ten pak spustí reduce fázi)
	- master uzlům, které pověří reduce fází, dá informaci o klíčích + o tom, kde data k daným klíčům najde (na jakém mapovacím uzlu)
### Reduce fáze
- rozdělená data (v regionech) agreguje a kombinuje a vrací finální výsledek
	- nejdříve si natáhne všechny relevantní data ze všech regionů z příslušných Map serverů
	- pak si data mergne dohromady a seřadí si je podle klíčů
- agreguje výsledky z Map fáze podle klíče
	- jako vstup je klíč z Map fáze + všechna data spojená s tímto klíčem
	- pro každý unikátní klíč provede např. součet|maximum|sloučení dat apod.
- výstupem je sada klíč - finální hodnota

Idea je taková, že se chce výpočetní výkon co nejvíce distribuovat (jedná se tady o obrovské množství dat) a zároveň minimalizovat posuny dat po síti (to zabere hodně času).
***
# Detaily fungování
- master-slave architektura
	- master - přiřazuje jednotlivé tasky jednotlivým nodům (=slaves)
		- celý MapReduce Job je poslaný na mastera
			- pošlou se implementace Map/Reduce funkcí, popis vstupních souborů a popis výstupní složky
		- master zjistí (nebo ví), na jakých slaves je uložený obsah (splity obsahu), který je potřeba zpracovat
			- Map Task = zpracování 1 splitu 1 workerem
		- zároveň má všechna metadata o tom, kde jsou jaká data uložená atd. (metadata jsou uložená v distribuovaném file systému)
	- slaves (=workers) - mají uložený skutečný obsah dat
		- data jsou uložená v menších částech (= splitech)
		- vykonávají tasky poslané od mastera (Map task, Reduce task)
- pokud nastane chyba
	- chyby jsou v distribuovaných systémech velmi běžné
	- na workerovi (master periodicky pinguje jednotlivé workery, takže se to dozví)
		- pokud failne, tak master zresetuje jeho tasky a začne je posílat na jiné workery
	- na masterovi
		- dvě strategie:
			- většinou se nic neřeší (je to celkem ojedinělá událost) a vyřeší se to tím, že uživatel bude muset spustit celý MapReduce spustit znovu
			- nebo se dělají pravidelné checkpointy (zálohy), failure se detekuje a ze zálohy se vytvoří nový master node, který pokračuje v práci
- funkce Combine
	- funkce navíc, která se někdy hodí pro zmenšení objemu dat, který se musí posílat přes síť mezi Map a Reduce fází
	- provedou ji rovnou Mappery lokálně po Map fázi
	- je důležité, aby potom Reduce fáze byla:
		- komutativní, asociativní a idempotentní (tj. když uděláme tu samou úpravu 2x, tak se výsledek nezmění)
		- jedině tak se můžou data "předzpracovat" na Mapperech, jinak by toto přezpracování udělalo nepořádek v Reduce fázi 
- Counters
	- jednotlivé Joby mohou trvat opravdu dlouho, tak se hodí mít nějaké indikátory, že Job běží v pořádku a nic se např. nezacyklilo apod.
	- mohou být předdefinované (od MapReduce frameworku, např. počet dokončených Map Tasků/Reduce Tasků, počet zparsovaných input key-value párů atd.) či user-defined
- Stragglers
	- nodes, kterým výpočet trvá nějak dlouho (např. mají limitované zdroje, data jsou náročná apod.)
	- master vytvoří "back-up" tasky a zkusí je poslat i na další (volné) nodes a vezme si pak nejrychlejší výsledek - nemá smysl, aby jeden pomalý node blokoval celý výpočet
***
### Jednotlivé fáze v Apache MapReduce
- Vstupní data
	- data jsou uložena na [[Hadoop#HDFS|HDFS]] 
	- pomocí **InputFormat** je rozdělíme na jednotlivé **InputSplits**
		- což je reference na určitou část dat
		- počet splitů = počet tasků ve výpočtu = počet mapperů
	- **RecordReader** připraví key-value páry pro Mapper
- **Mapper**
	- zpracuje každý InputSplit a produkuje mezivýsledky (klíč-hodnota páry)
- **Combiner**
	- předzpracování dat na Mapperech a snížení objemu dat, které se budou přesouvat po síti
- **Partitioner**
	- vstupem je klíč-hodnota pár a rozhoduje o tom, do jakého Reduceru tyhle data půjdou
- **Shuffle a Sort**
	- přeskupí mezivýsledky podle klíčů (tak, aby byla data se stejnými klíči na stejných nodech) podle komparátoru klíče
	- tedy je kritická část pro síť, protože se data přemisťují přes síť (a může dojít k různým problémům)
- **Reducer**
	- aplikuje agregační funkce na data se stejným klíčem (jsou přeskupená a seřazená z předchozí fáze)
	- a výsledek je vlastně jedna jediná hodnota pro všechny páry se stejným klíčem (většinou nějaká agregace)
- Zápis dat (**OutputFormat**)
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdzJtTEudST5IQMaG9dIKk--OEBmGY0jlyPHB5qON0ow1Bl6puUfnyXRCNNKQuffXqaFPsWry60uFIUzGca1TW2k478ceqCjqaXGfn281l10U5UJU3EGIl8RPRl1XscsVqSepaaF1M-WzPX01AvixXrPEE?key=yGg6q_DMLJu5fx9VlDS_rQ)
***
### Příklady použití
- analýza logů, agregace dat, indexace obsahu, počítání výskytů slov, singulární rozklad velkých matic
- business využití:
	- risk modelling, customer churn
		- e.g. analysing the customer churn (= rate of losing customers) between phone operators (many of them change regularly due to different offers)
	- recommendation engine, customer preferences
- počítání počtu přístupů na určitou URL (vstupem je log HTTP požadavků daného serveru)
- invertovaný index (vstupem jsou textové dokumenty a já pro každé slovo chci seznam dokumentů, ve kterých se nachází)
- distribuovaný sort
	- shuffle fáze (pomocí Compare funkce) automaticky výsledky sesortí a Reduce fáze pak už nic nedělá
- reverse web-link graph
	- pro každý outgoing link se vloží key-value hodnota (target link, link aktuální stránky) - je to vlastně prohozené a takhle jednoduše mohu zjistit, jaké stránky na mě odkazují (to se právě hodí Googlu na ten [[Page Rank a HITS|Page rank]])
- tento přístup není moc vhodný pro úlohy, které vyžadují opakované iterace (např. strojové učení) - tam je lepší [[Apache Spark]]
