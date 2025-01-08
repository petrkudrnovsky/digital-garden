Proč ji děláme?
- abychom si vyjasnili zadání se zákazníkem
- zase zpřesnili odhad pracnosti projektu
- vymezili hranice systému, který budeme vytvářet (abychom toho nedělali moc - co není domluvené a zase abychom udělali všechny podstatné části pro klienta)
- chci definovat omezení kladená na systém

Požadavky mají 2 hlavní kategorie: [[#1) Funkční požadavky]] a [[#2) Obecné (nefunkční) požadavky]]
Jiná kategorizace požadavků (FURPS):
![[Pasted image 20230520200231.png]]

### 1) Funkční požadavky
- požadavky ohledně funkčnosti aplikace, co bude všechno umět a splňovat

### 2) Obecné (nefunkční) požadavky
- určují různá omezení kladená na systém
- mají velký vliv na návrh architektury aplikace
- určují dodržování standardů

Jaké informace by měl mít správný a přehledný požadavek?
1) název požadavku
2) zkratka (aby se na něj dalo rychleji odkazovat)
	- my jsme měli F1, F2 nebo N1, N2 - ale možná by bylo přehlednější mít nějaké zkratky či krátké názvy (někdy jsem se v tom ztrácel)
3) ==popis==
4) kategorie
5) priorita - dá se určovat podle [MoSCoW](https://en.wikipedia.org/wiki/MoSCoW_method)
6) složitost (většinou na škále od 1-10)

Dále by požadavek měl být jednoznačný, splnitelný a ověřitelný - tedy by splnění požadavku mělo být ověřeno v rámci akceptačního testování 
- Je třeba se vyhnout požadavkům, které jsou obecné a v podstatě nic neříkající

#### Jak a kde získám požadavky?
- z komunikace se zákazníkem (co požaduje, co se mu líbí, co by se mu hodilo)
- z [[Modelování obchodních procesů|modelu obchodních procesů]] 
- ze zadávací dokumentace (pokud už existuje)
- v případě již existujího systému - reportované chyby, neošetřené věci, věci, co by se daly zlepšit
- pozorování uživatelů při jejich práci, aktivní dotazování se
	- uživatelé náš systém pak budou používat, ti jsou pro nás nejdůležitější

Požadavky mohu definovat textově (jako seznam požadavků s uvedenými informacemi) + [[Modelování případů užití (Use Cases)|případy užití (Use Cases)]]
