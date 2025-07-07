> [!tldr] TL;DR
> Docker je open-source platforma, která umožňuje spouštět aplikace v definovaném prostředí (závislosti, knihovny, konfigurace) nezávisle na zdrojích a infrastruktuře hardware.
> Jedná se o jednu z forem virtualizace.
# Kontejner
- je to hlavní část Dockeru
- obsahuje samotnou aplikaci (s prostředím: závislosti, knihovny, config) a izoluje ji od infrastruktury
- největší výhoda: lze je přenášet (na mém počítači a na vzdáleném serveru se bude aplikace chovat stejně)
- technické základy:
	- Docker používá [namespaces](https://docs.docker.com/engine/security/userns-remap/) pro oddělení jednotlivých kontejnerů (a každý si myslí, že má zdroje jen pro sebe), cgroups pro oddělení a omezení zdrojů pro jednotlivé kontejnery a UnionFS jako abstrakci nad filesystémem, který mu umožňuje vytvářet volumes, mountovat složky do/z kontejneru apod.
- jsou vrstvené - tvoří se z tzv. předpisu (= Image), který obsahuje instrukce pro vytvoření kontejneru
	- Image je definovaný v souboru `Dockerfile` - první řádek identifikuje Image ze kterého se má začít
	- jeden řádek v `Dockerfile` je jedna vrstva 
		- a kontejnery mohou základní vrstvy mezi sebou sdílet (šetří se místem)
		- a jednotlivé vrstvy se mohou cacheovat, pokud už ji mám, tak se nasdílí místo nového stahování
	- příkazy instalují balíčky, kopírují soubory, spouští programy, kompilují apod.
	- mohu definovat proměnné prostředí a rovnou spustit program
### Rozdíl proti virtuálním strojům
- pomocí Dockeru jsme schopni dosáhnout virtualizace stejně jako s virtuálními stroji (VMs)
- výhoda kontejnerů je to, že "simulují" na aplikační vrstvě - to jim umožňuje oddělit aplikace od sebe, ale zároveň všechny využívají společný OS kernel (každý kontejner běží jako oddělený uživatelský proces)
	- velikostně jsou v řádu desítek MB
		- šetří se místem i díky vrstvám
	- neobsahují jádro OS, což snižuje režii
- narozdíl VMs slouží jako abstrakce fyzického hardware a jedna jednotka obsahuje celý OS s binárkami, závislostmi a samozřejmě finální aplikací
	- hypervizor je software, který pro jednotlivé VMs simuluje hardware (aby si VMs mysleli, že běží na počítači sami) a odděluje fyzické prostředky stroje od VMs
	- velikostně jsou v řádu GB a startují mnohem pomaleji než lehké kontejnery
- 
![[Pasted image 20250331140312.png]]
### Orchestrace kontejnerů
- tím, že můžeme projekt rozdělit na více aplikací (microservices, databáze, hlavní část apod.), tak je potřeba řešit mimo vzájemné spolupráce (přes nějaké API) taky rozdělení využití hardwarových prostředků
	- to zajišťuje třeba Cubernetes nebo Docker Swarm - ty nasazují na klastr serverů
	- Docker Compose - nasazuje na jednom stroji
		- nástroj pro definici a spouštění aplikace o více kontejnerech
		- popis v jazyce YAML
### Distribuce kontejnerů
- kontejner Dockeru není jenom forma virtualizace, ale je to i způsob distribuce software
	- obsahuje všechny závislosti ve správných verzích (někdy obsahuje i celý základ distribuce Linuxu)
	- nahrazuje .tar.gz, .war, .deb, .rpm
- je možné je distribuovat přes [[#Registry kontejnerů]]
### Registry kontejnerů
- = repozitáře obrazů kontejnerů (images)
- jsou veřejné (Docker Hub, Quay.io, Tutum.co) a na nich je možnost i privátních repozitářů (placené, pro menší firmy)