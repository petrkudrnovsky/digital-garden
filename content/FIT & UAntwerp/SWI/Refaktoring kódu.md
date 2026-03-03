- změny prováděj v malých krocích
- refaktoring je změna vnitřní struktury kódu, aniž by bylo změněno jeho vnější chování
	- je nutné už mít testy, abych mohl kontrolovat, že jsem vnitřní změnou nezměnil vnější chování
### Kdy bych měl začít s refaktoringem?
- objevují se duplicity
- mám dlouhé metody
- mám velké třídy a nízkou soudržnost [[GRASP - Vysoká soudržnost]]
- příliš mnoho parametrů
- nedodržené jmenné konvence
- složité struktury podmínek
### Vybrané techniky
- přejmenovávání metod (na lepší názvy)
- zapouzdření atributu
- nahrazení dědičnosti delegováním
	- rozbití striktní dědičnost-vazby
- nahrazení chybového kódu výjimkou
- extrahování metody (např. rozdělení do více metod, do služby atd.)
- skrytí metody
- zavedení Null objektu
- zavedení objektu jako parametru