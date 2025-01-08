Narozdíl od řádkových (= klasické relační) databází ukládají jednotlivé záznamy po sloupcích. To je výhodné, když potřebuji zpracovávat a analyzovat opravdu velké množství dat.

V podstatě to funguje tak, že jednotlivé hodnoty ze stejného sloupce jsou uloženy na disku sekvenčně vedle sebe - tedy se k nim dá velmi rychle dostat.

*Pořád je logicky struktura dat po řádcích, akorát rozdíl je ve způsobu ukládání dat, který je výhodnější právě pro určité úlohy a každý řádek může mít rozdílný počet sloupců*

| ID Number | Last Name | First Name | Bonus |
| --------- | --------- | ---------- | ----- |
| 534782    | Miller    | Ginny      | 6000  |
| 585523    | Parker    | Peter      | 8000  |
| 479148    | Stacy     | Gwen       | 2000  |
- v **řádkové** jsou data uloženy takto: `534782, Miller, Ginny, 6000; 585523, Parker, Peter, 8000; 479148, Stacy, Gwen, 2000`
- ve **sloupcové** jsou data uloženy takto: `534782, 585523, 479148; Miller, Parker, Stacy; Ginny, Peter, Gwen; 6000, 8000, 2000`

Existují "super sloupce", které jsou složené z více sloupců 
- např. super sloupec Adresa, složený ze sloupců Město, PSČ, Ulice atd.
### Výhody
- rychle se dostanou ke konkrétním hodnotám konkrétního sloupce (nemusí číst nerelevantní data z jiných sloupců)
	- tím pádem je možné dělat rychlé agregace (součty, průměry atd.)
- tím, že jsou za sebou uložené hodnoty jednoho sloupce, tak všechny mají podobný typ nebo formát -> dobře se **komprimují**
- řádkové databáze musí mít oindexované sloupce, aby se mohly lépe dostávat k sloupcovým datům - to zde není potřeba (= self-indexing), šetřím místo na disku (které by jinak indexy zabíraly)
### Nevýhody
- pokud upravuji či mažu řádky, tak mají nižší výkon
	- je potřeba odstranit hodnoty ve všech sloupcích - a hodnoty jednoho řádku nejsou za sebou 
- když mám složité dotazy, tak jsou méně flexibilní
### Kdy chci použít sloupcovou databázi?
- když mám velké objemy dat, které se moc nemusí měnit (aktualizovat)
- když chci analyzovat data pomocí agregačních funkcí
### Příklady
[[Apache Cassandra]]
Amazon DynamoDB
Apache Hbase
