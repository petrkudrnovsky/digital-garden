HTML5 přidává spoustu nových inputů pro
- email, telefon, URL, datum, čas, číslo, range ([MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input))
- nové atributy: required, pattern, min, max, placeholder, autocomplete

Ke každému inputu přidáváme jeho `<label>` i když nebude ve výsledku vidět - pro asistivní technologie a z hlediska sémantiky
- přiřadím ho k inputu buď pomocí ID inputu a `for` atributu na labelu
- nebo tak, že input obalím daným labelem

Když se dá heslo do type="password", tak se do toho PHP souboru posílá v podstatě v plain textu (pokud neproběhne nějaká kontrola na straně klienta před odesláním), takže posílat jenom přes HTTPS

Buttony mají type submit, reset a button (https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button#type)

Placeholder slouží k UKÁZCE vstupu - není to náhrada za label
### Radio buttons
- když jim dám stejný name, prohlížeč zajistí, že může být v jednu dobu aktivní pouze jeden
### Checkboxes
- pokud chci odeslat více checkboxů najednou se stejným name, tak ho musím napsat se suffixem: colors\[\]
### Select (výběrové pole)
- pomocí `<select>` a `<option value=X>`
### Odeslání dat
- method="GET"
	- hodí se na vyhledávání a filtrování
	- x-www-form-encoded - zakóduje pomocí '%' - tedy např. mezera je %20
	- nesmí mít vedlejší efekty
	- data se připojí k URL (pouze ASCII data a délka URL je omezená)
- method="POST"
	- data se posílají v těle požadavku
	- takhle se mohou posílat i binární data
	- readonly data se odešlou
	- disabled data se neodešlou vůbec
### Zpracování formuláře
1) identifikovat successful controls
	1) např. disabled nemůže být successful, zaškrtnutá pole jsou successful atd.
2) vytvořit "form data set" (dvojice klíč-hodnota, name=value)
3) zakódovat data
4) odeslat data na požadovanou URL
- v HTML5 je to mnohem složitější

Data z formuláře by se ideálně měly validovat jak na klientovi, tak i na serveru, ale na serveru určitě!
### Jak se řeší vícenásobné odeslání dat
- při načtení stránky s formulářem se vytvoří token, který se uloží do [[Session]] a do skrytého pole formuláře
- hodnota toho skrytého pole se pak pošle společně se submitnutím toho formuláře a na serveru se tokeny porovnají, pokud se nerovnají, tak je požadavek neplatný
	- poté se token odstraňuje ze Session, tedy druhé odeslání formuláře už bude odmítnuto
	- pokud při kontrole token vůbec neexistuje, je požadavek také neplatný


