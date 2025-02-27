Uživatel se musí nejdřív autentizovat, aby měl přístup k systému. Poté autentizovaného uživatele můžeme ve webové aplikaci přiřazovat k jednotlivým akcím. Ověřuji, jestli uživatel je skutečně tím, za koho se vydává. To se děje pomocí několika způsobů.
### Jaký je rozdíl mezi autentizací a autorizací?
#### [[Autentizace]]
- proces ověření identity uživatele, zjišťujeme, jestli uživatel, který chce získat přístup k systému (nebo jeho části) je skutečně osobou, za kterou se vydává
#### [[Autorizace]]
- určení, které části systému jsou pro konkrétního uživatele přístupné a které ne
- určuje to, co může/nemůže uživatel dělat po tom, co byl úspěšně autentifikován
### Základní rozdělení metod autentifikace
Uživatel:
1) something you know - jméno/heslo, ověřovací otázka
2) something you have - telefon (SMS, autenticator aplikace, tokeny (USB, smart card), certifikát)
3) something you are - otisk prstu, snímek duhovky, DNA
### Problémy jednodušších vs. pokročilejších metod
- jednodušší:
	- jméno je většinou známé
	- uživatelé jsou líní (jednoduchá hesla, jednoduché odpovědi na otázky)
	- dají se odhadnout (předvídatelnost), slabší bezpečnost
	- přístupové údaje se dají snadno získat [[Phishing|phishingem]]
- pokročilejší
	- je potřeba speciální zařízení (telefon, čtečku otisků, kameru, token)
	- jsou dražší (potřeba poslat SMS, koupit USB token)
	- důvěra ve třetí strany (např. v ověření identity)
	- je to celkově složitější a méně pohodlné pro uživatele
### HTTP Basic ([MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication))
![[Pasted image 20231205143623.png|500]]
- nativně ve většině prohlížečů
- POZOR, údaje (i hesla) se posílají jako plaintext - vždy používat HTTPS
	- jsou zakódované v base64 (to ale není bezpečné šifrování)
### HTTP Digest 
- bezpečnější než [[#HTTP Basic ([MDN](https //developer.mozilla.org/en-US/docs/Web/HTTP/Authentication))|HTTP Basic]]
- údaje se posílají už zašifrované hashovací funkcí + nonce (ochrana proti 
[[Bezpečnost webových aplikací#Replay attack|replay]] útokům) a další kontrolní atributy
- hashuje se pomocí MD5, takže už také to není bezpečné (je nutné používat HTTPS)
### Přihlašovací formulář
- implementace je uzavřená (dělám si ho sám, tak si ho můžu i sám zabezpečit)
- heslo je potřeba vždy ZASOLIT a ZAHASHOVAT (např. bcrypt)
- samotná data se posílají nezabezpečeně => zase použít HTTPS
- pak si informaci, že se uživatel přihlásil uložím do cookie:
	- uložím si pouze sessionID (musí být unikátní) [[Session]], žádné další informace
- možné přidat CAPTCHA mechanismus pro ověření, že se přihlašuje skutečná osoba
### API klíč
- vhodné i pro autentizování dalších aplikací, požadavků přicházejících přes API apod.
- vlastnosti API klíče:
	- náhodný (jiný pro každého uživatele či aplikaci)
	- neuhodnutelný
	- odvolatelný (musí existovat možnost mu zrušit platnost)
	- přes HTTPS
- vzhledem k API klíči se dají nastavit i práva (př. read-only klíč)
	- používá se k autentizaci i [[Autorizace|autorizaci]]
- server pak ověřuje klíč při každém požadavku - API klíč je součástí komunikace
### OAuth
- jedná se o službu 3. strany
- princip autorizačního serveru a přístupových tokenů
### Více-faktorové ověřování
- kombinace více faktorů ověření a autentizace
- vhodné, když mám více úrovní aplikace (které požadují různé úrovně bezpečnosti)
- je potřeba hledat kompromis (ne každý uživatel chce strávit X minut jenom autentizací)
