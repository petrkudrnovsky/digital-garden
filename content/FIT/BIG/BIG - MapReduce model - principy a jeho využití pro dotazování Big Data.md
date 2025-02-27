[[MapReduce]]

> [!question]- Co je MapReduce model, kde vznikl a k čemu je dobrý?
> Jedná se o programovací paradigma pro zpracování velkých objemů dat v distribuovaných systémech. Jeho síla je v rozdělování složitých úloh do menších, paralelně zpracovatelných částí.
> 
> Princip je takový, že chceme přivést výpočet k datům a ne data k výpočtu (přesouvání velkého množství dat po síti). Využít to můžeme např. pro analýzu logů, agregaci dat, indexování obsahu, počítání výskytů slov
> 
> Více viz [[MapReduce]]
> 
> Vznikl v Googlu v rámci nástroje [[Hadoop]]

> [!question]- Jak funguje MapReduce model? Jaké má fáze a jak fungují v rámci Apache MapReduce?
> Viz [[MapReduce]]

> [!question]- Krátce, co je Hadoop? A jakou roli v něm má MapReduce?
> Viz [[Hadoop]] a [[Hadoop#MapReduce]]

> [!question]- Jaký framework pro distribuované výpočty ještě znám? Jaké má výhody oproti MapReduce?
> [[Apache Spark]]
> Přístup MapReduce není dělaný na výpočty, kde je potřeba hodně opakovaných iterací (např. strojové učení) - na to je lepší Apache Spark
> Apache Spark je vylepšená verze Apach MapReduce + nemusí běžet na [[Hadoop#HDFS]]
