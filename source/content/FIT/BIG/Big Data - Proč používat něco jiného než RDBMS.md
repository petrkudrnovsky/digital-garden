Relační databáze jsou takový švýcarský nožík - umí (skoro) všechno. Ale v dnešním světě, kdy je dat obrovitánské množství a možnosti využití jsou fakt široký, tak se hodí mít i jiné databázové stroje, které dokáží určitá data zpracovat efektivněji.

Rysy relačních databází
- musím pro každou novou informaci přidávat nový sloupec (každá změna ve struktuře vyžaduje zásah)
- datový typ musí být atomický (objektově relační DB už to umí)
	- nejde jednoduše definovat komplexní objekty (a obecně user-defined objekty)
- pro chybějící hodnoty je tam null (takže to pak bobtná a bobtná)
	- mohu mít velké (ale vlastně celkem prázdné) tabulky
- relační databáze
	- navržena tak, aby minimalizovala redundance a minimalizovat aktualizační anomálie
	- chci je mít v normalizovaném stavu
	- není to pak vhodný na agregace dat
		- aggregate ignorant approach - je to výhoda i nevýhoda
			- tyto systémy prioritizují konzistenci dat, strukturu
- vyžadují striktně [[ACID]]

Fenomén [[Big Data]]
- 5x V
- NoSQL
	- technologie, které se nějak snaží kompenzovat nějaké problémy RDBMS (ale vždycky za cenu něčeho jiného)
		- většinou se hodí na nějaký specifický use-cases <=> RDBMS jsou takový "univerzální" - one-size-fits-all
	- můžeme pracovat nad 
		- [[BASE model]] - ("zásada"), relaxovaná
			- basically available, soft state, eventually