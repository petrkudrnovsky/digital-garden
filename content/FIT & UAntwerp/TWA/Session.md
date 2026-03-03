- je to klientovo úložiště na serveru nebo v databázi
	- většinou jde o neperzistentní úložiště (paměť serveru), tj. po restartu serveru jsou informace ztraceny
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
- [[Cookies]] také nejsou ideální pro přenášení Session ID
	- nemáme jistotu, že si uživatel nesmazal Cookies
	- uživatel si může sám vytvořit vlastní Cookies
	- kvůli tomu se přidávají další bezpečnostní prvky (Referer, User-Agent, IP adresa)
### Implementace
- pomocí souboru, relační databáze nebo [[Key-Value databases]]
### Proces
- na první request se server podívá, jestli existuje Session, pokud ne, tak ji vytvoří a klientovi pošle `Set-Cookie` s hodnotou SessionID
- klient si pak uloží hodnotu SessionID u sebe v paměti a pak ji posílá společně s každým dalším requestem

Typ útoku: [[Bezpečnost webových aplikací a různé útoky#Session stealing|Session stealing]]
