Dokumentace: https://www.typescriptlang.org/
Cheatsheet: https://www.typescriptlang.org/cheatsheets
Handbook: https://www.typescriptlang.org/docs/handbook/intro.html
Playground: https://www.typescriptlang.org/play
***
Superset jazyka [[Javascript]]. Hlavním cílem je dát JavaScriptu datové typy (ale pozor, není to kompilátor, nejde to vynutit to 100%, takže to pořád jde ohnout).

```sh
npm install typescript --save-dev # pridani do projektu
tsc --init # init a vytvori se tsconfig.json
tsc # kompilace
	--traceResolution # vypise verbose log, co kde nachazi (debug, pokud mi to nebere soubor)
	--noResolve # 
tsc -w # automaticka kompilace on-save
```

### TS Config (tsconfig.json)
- konfigurace pro kompilátor TS
- kompilátor do čistého JS pro prohlížeče
- lze ho nastavit tak, aby vytvořil kód i pro staré prohlížeče atd.
	- lze ho využít jenom pro to, když mám moderní JS kód a chci ho převést pro starší prohlížeče
	- pozor, pak to generuje celkem dost kódu

- **sourceMaps** - lepší vizualizace TypeScriptu v dev tools (kvůli lepšímu debuggování - defaultně vidím kompilovaný JS)
- nastavení v tsconfigu
	- `extends` - lze použít už předpřipravené configy

**Typedoc** - komentářová dokumentace, vytvoří HTML stránku s dokumentací

Skript vs. Modul
- skript nemá `import a export` a proměnné jsou dostupné globálně ve všech .ts souborech
- modul má `import a export`