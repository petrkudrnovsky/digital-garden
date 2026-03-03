CI - continuous integration
- INTEGRACE kódu od vývojářů do společného repozitáře (automaticky sestaví aplikaci a otestují, a pokud kód projde, může se kód integrovat)
CD - continuous delivery (deployment)
- navazuje na CI a automatizuje proces přípravy do produkčního prostředí (vytvoření balíčku aplikace, který se pak nasadí)
## Proč?
- sestavení, testování a nasazení aplikace probíhá mimo počítač vývojáře na samostatném serveru
	- nezatěžuje to počítač vývojáře, který může po commitu ihned pracovat
	- odstraňuje to problémy spojené s různými vývojovými prostředími různých vývojářů (sestavení probíhá na pevně definovaném prostředí)
	- sestavení je automatické, což eliminuje chybu programátora při manuálním sestavování
		- definováno pomocí skriptů/konfigurací a specializovaných nástrojů
## Nástroje pro sestavení aplikace
- umožňují opakovatelné a automatické sestavení aplikace
	- tím, že mají předpřipravené build skripty pro sestavení aplikace
	- mohou obsahovat sadu úloh, postupů a doporučení pro sestavování aplikací
	- řeší závislosti na knihovnách třetích stran (podle konfigurace stáhnou potřebné závislosti)
		- podle např. composer.yaml, pom.xml atd. - jednotlivé knihovny obsahují konkrétní verzi ([[Balíčkovací systémy a sémantické verzování]])
- Maven
	- pro Javu, pro celý cyklus nasazení
	- konfigurace je v souboru: `pom.xml`
	- upřednostňuje konvenci před konfigurací (dodržením konvencí zjednoduším konfiguraci)
	- jeho možnosti rozšiřuje velká škála pluginů, každý plugin může mít několik cílů
	- spuštění pomocí `mvn package`
- Gradle
	- pro projekty v Groovy, Javě či Kotlinu, řeší nějaké nedostatky Mavenu
	- konfigurační soubory jsou v jazyku Groovy
	- `gradle init`, `gradle jar`, `gradle build`
- npm
	- pro projekty v [[Javascript]]
	- zajišťuje správu závislostí na externích balíčcích
	- konfigurace v: `packages.json`
	- `npm init`, `npm install`, `npm run build`
- Make
- dotnet
## CI/CD
- umožňuje spouštět úlohy mimo počítač vývojáře (na vzdáleném serveru)
- účel: sestavení, testování, nasazení aplikace, generování reportů atd. 
#### Pipeline
- sada automatických kroků, které jsou prováděny v rámci CI/CD
- je to automaticky spustitelný "popis" procesu
- skládá se z jednotlivých úloh: jobs, tasks, steps (záleží na nástroji)
	- jednotlivé úlohy se seskupují do fází (stages)
	- úlohy v jedné fázi mohou běžet paralelně (nezávisí na sobě)
	- fáze jsou sekvenčně za sebou
- spouštění
	- po změně zdrojového kódu
		- např. na speciálních větvích
	- v pravidelných intervalech
		- např. pro kontrolu zranitelností v použitých knihovnách (Dependency check)
		- pokud z výkonnostních/časových důvodů nelze provádět pipeline po každé změně
- jednotlivé joby mohou být spuštěny automaticky nebo manuálně (čeká se na uživatele než ji spustí)
- artefakty - výstupy z jednotlivých úloh
	- umožňují přenášet data mezi jednotlivými úlohami (jedna vytvoří balíček a druhá ho nasadí)
	- dají se sdílet v rámci dané pipeline (mimo pipeline se musí přes speciální úložiště)
#### Nástroje
- Gitlab CI, Bitbucket Pipelines, Jenkins atd.

