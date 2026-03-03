HTTP je bezstavový protokol
- to znamená, že požadavky (Requesty) jsou na sobě nezávislé, tedy server si při zpracování nového stavu nepamatuje, co bylo v předchozím Requestu
- mezi Requesty se na serveru neuchovávají žádné informace
- tedy si musím já vytvořit svoje mechanismy na vrstvě aplikace, které mi budou stavy držet 
	- nejčastější způsoby jsou Cookies, Sessions nebo skryté pole ve formulářích

- výhody pro servery
	- jednodušší na implementaci, server si pro každého klienta nemusí vlastně nic pamatovat
	- lepší škálovatelnost
		- je jednoduché při rostoucí zátěži přidávat další servery, protože Requesty jsou nezávislé a nemusí se řešit synchronizace mezi servery
		- jednoduchý load balancing - není potřeba od jednoho uživatele přeposílat na ten stejný server, protože by tam měl nějaký uložený stav
- nevýhody - pro lepší zážitek z webové aplikace (držet nákupní košík, přihlášeného uživatele atd.) musím používat jiné způsoby (cookies, session atd.)
### Stav je reprezentován v URL
- tímhle způsobem se dá stav aplikace jednoduše sdílet 
	- a může být řízený odkazy
- nehodí se pro důvěrný obsah
- dobré pro vyhledávání
### [[Cookies]]
### [[Session]]