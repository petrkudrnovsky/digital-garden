Řeší problém toho, že v modelech bereme jednotlivé termy jako oddělené, ale oni zapadají nějak do kontextu a ten také musíme brát v potaz. Jinak nám to bude dávat velkou podobnost zcela nesouvisejícím documentům, ale se stejnými či podobnými termy (použité v jiném kontextu).

LSI je rozšíření [[Vektorový vyhledávací model|Vektorového modelu]]. Definuje **concept-by-document** matici (concept je abstraktnější pojmenování nad termy - není to konkrétní term, ale třeba skupina podobných termů).
- tato matice je hustá a query vector je také hustý.

### Pros
- přináší sémantiku do collection
- vyhledávání není term-independent, ale je to concept-based
- řešení synonym a homonym
- redukce dimenze matic a vektorů

### Cons
- concepty jsou pořád jenom nějaké kombinace termů - ještě tam není pravá lingvistika
- invertovaný vektor se kvůli husté matici a query vectoru nedá efektivně použít
- výpočetně náročné preprocessování matice (SVD algoritmy)