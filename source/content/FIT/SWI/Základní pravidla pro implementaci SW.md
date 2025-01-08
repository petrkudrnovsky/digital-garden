### Správný návrh metody
- uplatním [[GRASP - Vysoká soudržnost|princip vysoké soudržnosti]] 
	- metoda má právě jednu přesně definovanou zodpovědnost
	- pojmenování: srozumitelné a samo-popisné (vyvarovat se And nebo Or v názvu)
- čím delší, tím větší chybovost
- max. 100 - 200 řádků
- v metodě volám jenom metody objektů (sebe, které vlastním, které vytvářím, které dostanu jako parametr)

### DRY - do not repeat yourself
- kód - zapouzdření funkčnosti do metody a použití na více místech
- data - správný návrh ukládání dat
	- používání pojmenovaných konstant - nemít číselné hodnoty v kódu
- ! neznamená to zrušit Copy + Paste - někdy stejné části kódu spolu logicky nesouvisí

### [[Rozhraní (Interface)]]
- všude, kde se očekávají změny nebo rozšíření
- rozhraní, které zveřejním (a už ho nějaké objekty používají) bych neměl měnit
	- můžu samozřejmě přidat další metody či měnit implementaci stávajících

### Dědičnost
- něměli bychom používat dědičnost jen proto, že 2 třídy mají společnou možinu atributů
- měl by platit **Liskov substitution principle**: 
	- pokud instanci rodiče nahradíme instancí potomka, tak pak by kód, který očekává instance rodiče měl fungovat i s instancí potomka
	- to platí např. pro stejné parametry a výstupní hodnoty
	- např. potomek by neměl v overriden metodě vyžadovat striktnější pravidla pro parametry (pak to se zavoláním kódu, který očekává rodiče, spadne)
- dědičnost je velmi silná vazba
- vícenásobná dědičnost často porušuje [[GRASP - Vysoká soudržnost|princip vysoké soudržnosti]]

### Štábní kultura psaní kódu
- pojmenovávání, srozumitelnost
- komentáře, odsazování
- dodržování daných konvencí psaní kódu (pro konkrétní programovací jazyk)

### Správný návrh > efektivita
- samozřejmě do jisté míry, ale není dobré to rvát na efektivitu - pak to bude sice o něco rychlejší, ale nepřehledné a špatně rozšiřitelné

### Maximalizovat znovupoužití existujícího kódu
- knihovny, frameworky

### Konfigurace do externích souborů
- pro usnadnění procesu nasazení v různých prostředích

### Popisy v GUI do externích souborů
- pak mohu přidat lokalizaci

### Ošetřování
- nevalidních vstupů (ze souborů, od uživatele, z externího API)
- před chybami ostatních programátorů (tj. i když je to metoda pro vnitřní použití, tak by měla být ošetřená)
- vracení správných chybových kódů
- vyvolávání a chytání výjimek
- správné ukončení programu

### Logování
- rozdělení podle úrovní/priorit, pěkné formátování + uložení na určité místo
- DEBUG, INFO, WARN, ERROR, FATAL, apod.