Vstupní nástroj do celého [[Elasticstack]]

Spravuje, sbírá, obohacuje a předzpracovává logy a pak je posílá do [[Elasticsearch]]

Centralizuje data z různých zdrojů (logy, databáze, API atd.)
Zpracovává data (transformuje, obohacuje, filtruje...)
Podporuje různé formáty (JSON, CSV, XML atd.) - na to má různé pluginy
Je multiplatformní (běží na JVM)
### Pipeline
- má 3 části
	- input - sběr dat ze zdrojů
	- filter - zpracování dat
	- output - odeslání do cíle ([[Elasticsearch]], soubor, e-mail)
- na jednotlivé části má svoje pluginy
### Event Object
- hlavní objekt, který zapouzdřuje tok dat v pipeline
	- uloží do něho vstupní data i další pole ve fázi *filter*
### Nevýhody
- je náročný na výkon a na paměť
- složitě se nastavuje