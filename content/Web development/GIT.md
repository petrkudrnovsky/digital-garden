Zde si ukládám šikovné git příkazy, abych je měl na jednom místě.
- [[GIT - vracení změn]]
- [[GIT - checkout remote větve]]
- [[GIT - divergent branches when pull]]
- [[GIT Blame - aneb, kdo to udělal]]
#### Situace: chci pushnout novou lokální branch na origin (aby se trackovala)
```
$ git checkout -b new_branch
$ git push origin -u new_branch
# nebo je možné: $ git push origin --set-upstream new_branch
```
#### Situace: mám na lokálu X změn, které nemají content (pravděpodobně jenom nějaké line endings) a chci to přepsat z originu
```sh
$ git fetch --all # update informací z originu
$ git branch backup-branch-name # pro jistotu rychlý backup
$ git reset --hard origin/master # reset master větve
```
- pokud si chci zachovat lokální commit, který ještě není na serveru
```sh
$ git checkout master
$ git branch new-branch-to-save-current-commits
$ git fetch --all
$ git reset --hard origin/master
```
### Git Flow
- robustní a složité (nevhodné pro menší projekty)
- hlavní jsou master a develop
	- master - zdrojové kódy ready do produkce (commity mají jednotlivé tagy verzí)
	- develop - příprava kódů a features pro nasazení
	- podpůrné větve:
		- feature - nové funkčnosti (vznik z develop, sloučení s develop)
		- release - příprava do produkce (master)
			- vznik z develop, proběhne příprava a sloučí se s develop a master
		- hotfix - opravy kritických chyb
			- vznik z master a slučuje se do master a develop
- vhodné, když chci mít více verzí produkce
![[Pasted image 20240106113455.png|500]]
- Gitlab článek, jaké jsou s tím problémy: https://about.gitlab.com/blog/2020/03/05/what-is-gitlab-flow/
### Github Flow
- zjednodušené [[#Git Flow]], zaměřené na rychlé doručování, pro projekty, kde je častý release a je potřeba flexibilita
- master a feature větve
- platí to, že cokoliv je na masteru může být ihned deploynuto
- ideální pro jednu verzi produkce
- absence release plánování, neřeší deploy, prostředí atd.
![[Pasted image 20240106115702.png]]
### Gitlab Flow
- komplexnější než [[#Github Flow]] 
- má větve podle různých prostředí: testing, staging a production (tzv. environment branches)
	- mergem do těchto větví se nasadí nová verze do těchto prostředí
- master - produkční kód
- podporuje CI/CD
![[Pasted image 20240106115630.png]]
### One Flow
- alternativa k [[#Git Flow]]
- postaveno na principu, že každá nová verze produkce musí být postavena na té předešlé
- popis přímo od stvořitele Adama Ruky: https://www.endoflineblog.com/oneflow-a-git-branching-model-and-workflow
	- zároveň zmiňuje, proč je [[#Git Flow]] špatné