#### Vrácení staged změn
```shell
git reset
```

#### Vrácení posledního commitu
```shell
git reset --soft HEAD~1
```
- `--soft` znamená, že se zachovají změny v mém working directory, takže je mohu změnit a znovu commitnout