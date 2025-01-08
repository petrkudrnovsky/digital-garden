Větší část logiky se vykonává na straně klienta, klient při načtení obdrží skoro celou aplikaci a data si aplikace získává pomocí API.

Frameworky (AngularJS a další) se snaží většinu logiky přesouvat na klienta
- celá aplikace je pak na straně klienta - takže dokáže fungovat i bez přístupu i internetu (dokud nepotřebuje akci z BE)
- ale je potřeba BE část (např. API), kvůli perzistenci dat apod.
- zajišťují routování, tracking historie, perzistenci dat na straně klienta, komponenty

React už padá, [[Vue.js]] je teďko populární (ale nebude na dlouho)

Teďko se zase zpátky přiklání k server-side renderingu (takže přesun většiny aplikace na server zpátky)
### SPA výhody
- výhodou je zacachování statických dat (jenom jednou se načtou celé)
- rychlejší respozitivita
	- přijdou jen potřebná data, překreslí se jen část aplikace (díky zvládnutému [[AJAX]])
- uživatelsky přívětivé
### SPA nevýhody 
- to že to jde, to neznamená, že se to dělá (dělat server-side rendering pomocí JS frameworku)
- nějaké aplikace jsou JS-only, takže nejsou připravené pro čtečky např. pro slepé...
	- a když má uživatel vypnutý Javascript, tak se nezobrazí nic

- pomalé - když není optimalizované posílání dat
	- např. posílání formátovaných JSONů
		- lépe posílat neformátované, ideálně v binární podobě
- pokud píšu appku pro svůj server, můžu to optimalizovat
- pokud to píšu tak, že to poběží u klientů (mobil, notebook, hodinky, počítač atd.) - je horší optimalizace
- vyskytují se typické problémy [[Javascript]]/[[AJAX]]
	- většinu z nich vyřeší frameworky
	- browser history (pushState, replaceState)
- když přidám novou část/funkcionalitu, je potřeba přenačíst celou aplikaci (což se u SPA běžně neděje)
- horší SEO než MPA

Když vyvíjím aplikaci, nepočítej s tím, že to bude jednorázová aplikace - je potřeba být připravený na rozšiřování a udržování
- kvůli bezpečnosti - průběžné aktualizování balíčků
### Rozdíl oproti MPA (multi-page application)
- MPA se skládá z více stránek, mezi kterými se přechází pomocí odkazů, tedy při změně stránky se načítá ze serveru znovu celá stránka
- MPA jsou starší, tradičnější a nejsou tak uživatelsky přívětivé
- naopak, ale mohou být lepší v [[Search engine ranking a SEO|SEO]], protože každá stránka má svoji URL, název, metadata apod.
- jsou jednodušší na údržbu než SPA