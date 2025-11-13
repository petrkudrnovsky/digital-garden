---
Zdroj: https://www.freecodecamp.org/news/git-checkout-remote-branch-tutorial/
---
Nejdříve si fetchnu všechny nejnovější informace z originu:
```shell
git fetch origin
```

Pak si můžu vylistovat všechny dostupné branche (i z originu)
```shell
git branch -a
```

Nemůžu totiž dělat změny na větvi, která je jenom na originu, takže si musím u sebe vytvořit kopii té větve takto (kde `origin/fix-failing-tests` je větev na originu)
```shell
git checkout -b fix-failing-tests origin/fix-failing-tests
```

Pak normálně budu mít lokálně větev a pushlé commity se namapují na tu stejnou na originu.