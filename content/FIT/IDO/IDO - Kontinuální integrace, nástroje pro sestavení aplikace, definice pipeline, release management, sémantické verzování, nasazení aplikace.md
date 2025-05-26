Doplnění otázky: správa prostředí, přínosy kontejnerizace a používané nástroje.

[[Continuous Integration (CI) a Continuous Delivery (CD)]]
- CI/CD
- Nástroje pro sestavení aplikace
- Pipeline

[[Release a nasazení aplikace]] 
- release management
- nasazení aplikace
- správa prostředí

[[Virtualizace]]
- výhody konsolidace serverů (tedy virtualizace)
	- úspora energie, zlepšení využití serverů
	- vyšší flexibilita při různých úrovních využití aplikace
	- oddělení aplikací z hlediska jejich závislostí (tedy nemůže dojít ke kolizi závislostí na serveru)
	- možnost limitace výkonu aplikací
### Kontejnerizace
- 2 druhy kontejnerů
	- systémový
		- je blíž virtuálním počítačům, obsahuje OS (který musí být kompatibilní s jádrem hostitelského OS), má daemon init, další procesy atd.
		- LXC, OpenVZ atd.
	- aplikační
		- pro nasazování aplikací na server, na cloud
		- obsahují pouze 1 proces (a jeho potomky)
		- [[Docker]]
[[Docker]]
- přínosy kontejnerizace (a rozdíly oproti VMs)

[[Balíčkovací systémy a sémantické verzování]]
- sémantické verzování