Moderní způsob tvoření webových aplikací bez velkého [[Javascript]] overloadu a bez zátěže velkých FE frameworků.

Má 3 hlavní části:
### Hotwire Turbo
- hlavní část Hotwire
- je to nástroj pro rychlé načítání celých stránek a nebo jenom jejich částí asynchronně
- části
	- Turbo Drive - nahradí standardní navigace v prohlížeči a umožňuje rychlé procházení stránek bez plného načítání
		- nahrazuje HTTP Requesty za [[AJAX]] volání a pak prohodí obsah stránky
	- Turbo Frames - umožňuje načítat pouze části stránek (Frames)
	- Turbo Streams - přes [[Progressive Web App#WebSockets|WebSockety]] umožňuje přímo pushovat změny v reálném čase uživatelům
	- Turbo Native - integrace webových aplikací s nativními aplikacemi
### Hotwire Stimulus
- malý JS framework, součástí Hotwire
- doplňuje [[HTML]] kód malými funkčními částmi bez nutnosti komplexního JS frameworku
### Hotwire Strada
- komponenta Hotwire, propojení webových aplikací s nativními aplikacemi v mobilních zařízeních
