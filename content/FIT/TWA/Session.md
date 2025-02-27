- je to klientovo úložiště na serveru nebo v databázi
- pro každou session existuje unikátní ID - to se posílá většinou jako Cookie (nebo jako query parametr)
	- musíme přidat bezpečnostní prvky - uživatel si Session Cookie mohl smazat či nahradit
- při každé komunikaci prohlížeč pošle Session ID a server se pak podle toho může dostat k relevantním údajům pro daného uživatele
- uzavření session
	- Cookie vyprší, prohlížeč je zavřený, uživatel se odhlásí atd.
	- Session data na serveru se pak smažou
- jako query parametr to není ideální
	- protože se to pak musí vkládat do každého odkazu a při zkopírování a sdílení příjemce získává identitu odesílatele
	- když už se tam vkládá, tak je to lepší jako UUID
	- nehodí se pro důvěrný obsah
- cookie také není ideální
	- nemáme jistotu, že si uživatel nesmazal Cookie
	- uživatel si může Cookies vytvořit sám
	- kvůli tomu se přidávají další bezpečnostní prvky (Referer, User-Agent, IP adresa)
### Implementace
- pomocí souboru, relační databáze nebo key-value databáze

Typ útoku: [[Bezpečnost webových aplikací#Session stealing|Session stealing]]
