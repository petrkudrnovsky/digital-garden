### Function
- `SELECT my_function()`
- mají návratovou hodnotu, nemohou obsahovat `COMMIT` či `ROLLBACK` - fungují v rámci jedné transakce, ve které byly zavolané
- dá se volat v PL/pgSQL kódu (viz [[Postgres - extensions#PL/pgSQL]])
### Procedure
- `CALL jmeno_procedury()`
- vrací se jenom stav, jestli se povedla/nepovedla
	- návratové hodnoty se dají vrátit přes `OUT` nebo `INOUT` parametry
- dají se pomocí nich řídit transakce - mohu používat `COMMIT` a `ROLLBACK`
- můžu s nimi dělat větší transakční řízení, dávkové úlohy apod.
### Trigger
- mechanismus automatického spuštění kódu, když se stane nějaká akce či událost (např. `INSERT, DELETE, UPDATE...`)
	- trigger může spustit [[#Function]] před/po provedení triggrovací akce