- usnadňují správu knihoven pro moje projekty
- hlídají závislosti jednotlivých balíčků
- vždy je potřeba nějaký konfigurační soubor
	- pomocí něj se pak přidávají závislosti, instalují a aktualizují balíčky
	- `composer require|install|update|remove [--dev]`
- máme 2 config soubory
	- seznam balíčků - které jsou potřeba + jejich verze (flexibilněji zadané `^1.5` atd.)
		- může obsahovat pomocné skripty
	- aktuální sestava
		- informace o skutečně nainstalovaných souborech + jejich přesných verzích
		- běžně `.lock` soubor
- `vendor` určitě není potřeba verzovat
### Platformy
- Linux: apt, yum, dnf, YaST
- [[Web Development#PHP|PHP]]: composer
- node.js: npm
- [[Python]]: PIP
- Ruby: install

### Sémantické verzování
- https://semver.org/
`major.minor.patch`
- major - změny nejsou zpětně kompatibilní
- minor - přidání funkcionality se zachováním kompatibility
- patch - opravena chyba, se zachováním kompatibility

Jakmile se vydá verze, už se nesmí měnit.

**Pomlčka** pro verze před vydáním (př. př. 1.0.0-alpha.1)
**Plus** pro metadata (př. 1.0.0+20200101T170000)

Verze
- 1.2.7 - konkrétní hodnota
- >1.2 - větší než zadaná verze
- 1.0 - 1.3 - interval verzí
- 1.0.* - všechny verze >= 1.0.0 && < 1.1

- ˜1.2.3 všechny verze větší než uvedená a menší než další větší update (v tomto případě 1.3.0)
- ˆ1.2.3 všechny verze větší než uvedená a menší než další major verze (v tomto případě 2.0.0)