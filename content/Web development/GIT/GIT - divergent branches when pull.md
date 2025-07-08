Zdroj: https://salferrarello.com/git-warning-pulling-without-specifying-how-to-reconcile-divergent-branches-is-discouraged/

Hláška:
```bash
hint: You have divergent branches and need to specify how to reconcile them.
hint: You can do so by running one of the following commands sometime before
hint: your next pull:
hint: 
hint:   git config pull.rebase false  # merge (the default strategy)
hint:   git config pull.rebase true   # rebase
hint:   git config pull.ff only       # fast-forward only
hint: 
hint: You can replace "git config" with "git config --global" to set a default
hint: preference for all repositories. You can also pass --rebase, --no-rebase,
hint: or --ff-only on the command line to override the configured default per
hint: invocation.
fatal: Need to specify how to reconcile divergent branches.
```

Jde o to, že GIT neví, co má dělat s různými commity, které se mi tady potkaly. Existují 3 řešení, jak to vyřešit. Fast-forward tady nepůjde.
```bash
git pull --no-ff # vytvoří merge commit
git pull --rebase
```

V hintu jsou pak možnosti, jak takové chování nastavit globálně