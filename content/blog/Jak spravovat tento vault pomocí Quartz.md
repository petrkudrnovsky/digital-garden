---
title: Jak spravovat tento vault pomocí Quartz
date: 27. 2. 2025
draft: false
---
Hlavní složka pro moje poznámky je `content`, která funguje podobně jako `public` na webovém serveru. Na tuto složku mám namířený i Obsidian vault, kde spravuji veškeré poznámky, které chci, aby byly dostupné online.

Pro aktualizaci a synchronizaci poznámek s Githubem mi slouží příkaz:
```bash
npx quartz sync
```
- další informace zde: https://quartz.jzhao.xyz/setting-up-your-GitHub-repository

Lokálně si svoje Quartz stránky zobrazím pomocí:
```bash
npx quartz build --serve
```
- další informace zde: https://quartz.jzhao.xyz/build
# Template
- ve složce `templates/` mám šablonu, která obsahuje všechny dostupné properties, kterými mohu měnit název zobrazené poznámky, popis, aliasy apod.
- zajímavá možnost je property `draft`, pokud ji mám aktivní, tak se tato poznámka nesynchronizuje s webovou částí, je to způsob, jak mít některé poznámky soukromé