Jedna z možností je vyhledávat pomocí nějakého algoritmu na string-matching, ale to není vůbec efektivní a je to hodně pomalé. Mnohem lepší je využít invertovaný index a retrieval model (boolský či vektorový) či modely založené na pravděpodobnosti a předzpracovat a indexovat jednotlivé dokumenty

Základní definice:
- document - entita obsahující full text, který chceme prohledávat
- collection - množina documentů
- term - slovo či fráze, která se objevuje v documentu
- vocabulary - množina všech různých termů v documentu nebo v collection

Collection si musíme před vyhledávání předzpracovat (preprocessing)
- odstranění termů - stop slova (moc frekventovaná, čísla, jména atd.) - nemají moc rozlišovací hodnotu (moc mi ve vyhledávání nezúží výsledek)
- zjednušení existujících termů (go, goes, went, going -> go)
	- unifikace - stemming (přes kořeny slov) -> lingvistická magie
- díky tomu máme cca 20 % z původních termů - které už jsou dost vypovídající o tom fulltextu
	- menší nároky na uložení a rychlejší vyhledávání

Efektivitu měříme mírou spokojenosti uživatele s výsledkem vyhledávání.

Měříme **přesnost**: pravděpodobnost, že document ve výsledku je skutečně relevantní
- **False alarm (false hit)** - document neměl být ve výsledku, ale je tam
- v prohlížeči chci vysokou přesnost
Měříme **recall (úplnost)**: pravděpodobnost, že relevantní dokument je ve výsledku
- v prohlížeči úplnost tolik neřeším (nechci vyhledat 1 000 000 sedacích souprav)
- ale např. v medicínských aplikacích je úplnost velmi důležitá
- **False dismissal (false drop)** - document měl být ve výsledku, ale nebyl
Většinou chceme kompromis mezi těmito dvěmi přístupy. Také platí to, že pokud roste precision, tak klesá recall a naopak.

Řešení:
- [[Boolský vyhledávací model]] a invertovaný index
- [[Vektorový vyhledávací model]] a invertovaný index
	- [[Latent semantic indexing (LSI)]] - rozšíření vektorového modelu
- tyto modely jsou zaměřené na fulltextové vyhledávání a takhle pracovaly první vyhledávače
	- bohužel s nástupem internetu byly tyto metody degradovány spamovými stránkami, které obsahovaly přesně termy, kvůli kterým jim pak tyto modely dávaly vysoké ohodnocení