Je to metodika vývoje softwaru, která je alternativou k tradičnímu, ale pomalému vývoji aplikací (s pečlivým plánování a sekvenčním vývojem).

Soustředí se na rychlé vyvíjení prototypů a jejich iterativní vylepšování na základě zpětné vazby od uživatelů. Hodně se kooperuje s uživateli a jejich zpětnou vazbou.

V RAD se hodně používají nástroje, které takový rychlý vývoj podporují (ale zas něco za něco).

Velmi oblíbené v [[Agilní přístup|agilním přístupu]].
### Výhody
- kratší vývojový cyklus
- hodně flexibilní a schopný rychle reagovat na požadavky uživatelů
- zapojování uživatele do vývoje
### Nevýhody
- náklady za nástroje a zdroje mohou být vyšší
- není to vhodné pro všechny projekty, zejména pro velké a složité systémy
- nedostatečně se plánuje, takže je možné, že se může na něco zapomenout, není kvalitní dokumentace apod.

Čas se krátí také tím, že se nevyvíjí FE a BE zvlášť (např. proč mít validaci zvlášť na obou stranách, když může být jedna pro obě strany).

### [[CSS]]
- pro rychlejší vývoj stylování aplikace se používají frameworky
- např. Class-less CSS framework
	- začíná se tak, že se soustředí na [[HTML]] jenom a CSS styly se dodělají později - někdy to může být demotivující, protože hodně dlouho při vývoji vidíme jenom kostru a ne, jak to bude ve skutečnosti vypadat
	- ale pak dělat styly pro hotové a nepříliš se měnící HTML je rychlejší
- [[CSS#Frameworky|Frameworky]]
### [[Javascript]] support
- promítání Javascriptu do HTML a ulehčování nějakých funkcí v HTML
- např. jQuery, Alpine.js
	- sjednocovalo zápis nějakých věcí, dotazování atd.
	- většina věcí už je nyní nativně v JS nebo se řeší jinak
- taky urychluje vývoj aplikace, ale výsledně to není správně a hůř se to udržuje (pak by se to mělo refaktorovat)
### [[Hotwire]]
