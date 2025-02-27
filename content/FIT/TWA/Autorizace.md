[[Autentizace#Jaký je rozdíl mezi autentizací a autorizací?]]
### Edge-case: autorizace bez autentifikace
- pokud je umožněn anonymní přístup k nějakým zdrojům
- implicitní autorizace - základní úroveň přístupu pro všechny
	- a pak samozřejmě vyšší autorizace pro adminy apod.
- může být založeno na základě kontextu (např. IP, polohy, zařízení apod.) a ne na konkrétní identitě uživatele
### Typy
- geolokace (IP adresa, GPS)
- věk (dotážu se při vstupu)
- souhlas s licenčními podmínkami
- preference (jsem domácnost nebo firma)
- autentizace (a jaké jsou mi přiřazená oprávnění)
### Získání
- implicitní informace - geolokace, cookie
- explicitní - souhlas, věk, preference
- vícekrokové - [[Autentizace]]
### Implementace
- pomocí podmínek (není moc ideální)
- pomocí [[GoF návrhové vzory#Chain of responsibility|Chain of Responsibility]] (geolokace, věk, souhlas, autentizace)
### Role
- statické přiřazení práv
- struktura plochá nebo hierarchická
- Implementace pomocí rolí ve webové aplikaci - můžu se při zpřístupnění nějaké části ptát `$this->user->hasRole(Role::ADMIN)`
- řeší souhlasy, preference, autentizaci
### Voters
- v Symfony existují Votery, které rozhodnují, jestli má uživatel oprávnění něco udělat nebo ne
	- pokud ne, vyhazuje se výjimka
- řeší preference a autentizaci
- řeší přístup uživatele ke konkrétní instanci (např. editování svého článku a žádného jiného)
### ACL (Access Control List)
- správa a regulace přístupových práv ke zdrojům (soubory, služby, síťová zařízení...)
- komplexnější správa rolí, kombinuje se více konceptů (Role, Voters...)
- řeší autentizaci