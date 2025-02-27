 Kombinace technologií, AJAX engine, který se stará o zpracování požadavků
- není potřeba načítat změny jako celou stránku, ale jenom dílčí části
- nefunguje, když není zapnutý Javascript - proto tam chci mít ten `<noscript>` 
- spoustu nevýhod AJAXu vyřeší framework - ale nějaké tam zůstanou
### Jeho výhody
- zkrácení odezvy aplikace (protože se načítá jenom změněná část)
- menší množství přenášených dat v 1 požadavku
- úspora zdrojů na serveru
### Nevýhody
- komplikovanější práce s historií prohlížeče
- crawleři nevykonávají [[Javascript]], takže si části ovlivněné AJAXem nespustí
- když je vypnutý Javascript, tak velká část webu nemusí fungovat
- komplexnější kód
- vyšší počet požadavků
### Komunikace
Short polling - klient se neustále ptá ("mám nějaké nové články?") a server mu odpovídá
- nejjednodušší varianta - ale je náročná pro klienta i pro server

Long polling - timeout na serveru (doplnit)

Streaming - přímo implementované v rámci HTTP 

Chunked response - nahrazeno Streamingem (HTTP 1.1 -> HTTP 2.0) - [[HTTP protokol]]

Notification API, [[Progressive Web App#WebSockets|WebSockets]] a další
