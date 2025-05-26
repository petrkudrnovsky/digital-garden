[[Javascript]]
- [[Javascript - Asynchronní programování]]
	- vysvětleno, jak funguje callstack, web APIs, task queue a event loop dohromady
- [[Javascript - Iterátory a Generátory]]

### Shrnutí
- JS je single-threaded jazyk
- implementace asynchronního chování je přes Web APIs, které posílají definované callbacky na task queue, kde je Event loop nasadí do callstacku jakmile je prázdný
- hlavním stavebním kamenem je Promise, což je objekt, který provede nějakou akci a může skončit úspěšně (resolve) nebo neúspěšně (reject)
	- na Promise pak navazují konstrukce jako .then(resolve, reject), které specifikuje callbacky, které se právě zavolají po skončení Promise
	- navázat může i .catch()
