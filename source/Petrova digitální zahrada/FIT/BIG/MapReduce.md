> Jedná se o programovací paradigma pro zpracování velkých objemů dat v distribuovaných systémech. Jeho síla je v rozdělování složitých úloh do menších, paralelně zpracovatelných částí

[YT Computerphile](https://www.youtube.com/watch?v=cvhKoniK5Uo&ab_channel=Computerphile) 

Vyvinul ho Google v roce 2004 a implementován v nástroji [[Hadoop]]
### Map fáze
- rozdělí a předzpracuje data na menší části a ty pak posílá na zpracování na jednotlivé uzly
	- tedy rozdělí data a pak aplikuje nějakou funkci na každou část
- na každém uzlu se provede samotné mapování
- výstupem je sada dvojic klíč-hodnota
	- většinou je to nějaký klíč a k němu vypočtená hodnota
- *chci provést stejnou úpravu na datech, který mám rozfrkaný po celým clusteru - tak místo toho, abych ty data dal na jedno místo a provedl úpravu na jednom místě, tak tu úpravu provedu na X místech najednou*
### Mezifáze
- data ve formátu klíč-hodnota se pak třídí a seskupují podle klíče
- a pak se distribuují na společný redukovací uzel (pohromadě podle jednoho klíče)
### Reduce fáze
- rozdělená data agreguje a kombinuje a vrací finální výsledek
- agreguje výsledky z Map fáze podle klíče
	- pro každý unikátní klíč provede např. součet|maximum|sloučení dat apod.

Idea je taková, že se chce výpočetní výkon co nejvíce distribuovat (jedná se tady o obrovské množství dat) a zároveň minimalizovat posuny dat po síti (to zabere hodně času)
***
### Jednotlivé fáze v Apache MapReduce
Vstupní data
- data jsou uložena na [[Hadoop#HDFS|HDFS]] 
- pomocí InputFormat je rozdělíme na jednotlivé InputSplits
	- což je reference na určitou část dat
	- počet splitů = počet tasků ve výpočtu = počet mapperů
- RecordReader připraví key-value páry pro Mapper
Mapper
- zpracuje každý InputSplit a produkuje mezivýsledky (klíč-hodnota páry)
Partitioner
- vstupem je klíč-hodnota pár a rozhoduje o tom, do jakého Reduceru tyhle data půjdou
Shuffle a Sort
- přeskupí mezivýsledky podle klíčů (tak, aby byly data se stejnými klíči na stejných nodech) podle komparátoru klíče
- tedy je kritická část pro síť, protože se data přemisťují přes síť
Reducer
- aplikuje agregační funkce na data se stejným klíčem (jsou přeskupená a seřazená z předchozí fáze)
- a výsledek je vlastně jedna jediná hodnota pro všechny páry se stejným klíčem (většinou nějaká agregace)
- zápis dat (OutputFormat)
![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdzJtTEudST5IQMaG9dIKk--OEBmGY0jlyPHB5qON0ow1Bl6puUfnyXRCNNKQuffXqaFPsWry60uFIUzGca1TW2k478ceqCjqaXGfn281l10U5UJU3EGIl8RPRl1XscsVqSepaaF1M-WzPX01AvixXrPEE?key=yGg6q_DMLJu5fx9VlDS_rQ)
***
### Příklady použití
- analýza logů, agregace dat, indexace obsahu, počítání výskytů slov, singulární rozklad velkých matic
- tento přístup není moc vhodný pro úlohy, které vyžadují opakované iterace (např. strojové učení) - tam je lepší [[Apache Spark]]