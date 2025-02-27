Zdroj: https://gist.github.com/jesperorb/a6c12f7d4418a167ea4b3454d4f8fb61

Zde popíšu, jak pomocí AJAXu poslat data z HTML formuláře PHPčkovému skriptu, tam je zpracovat a pak ta data poslat (a na straně AJAXu je dostat zpátky).

Mám klasický formulář v HTML a trik je v tom, že pro odeslání dat po stisknutí submit tlačítka nepoužiji PHP soubor přímo v `action="handle_form.php"`, ale v JS souboru nasadím eventListener, který zavolá AJAXovou funkci, která vyřeší komunikaci se serverem (PHPčkem).

```js
const form = document.getElementById('exampleForm');
formBtn.addEventListener('click', function(event) {  
    event.preventDefault();  

    const formattedFormData = new FormData(form);   
    postData(formattedFormData);
});

async function postData(formattedFormData) {
	const response = await fetch('handle_form.php',{
        method: 'POST',
        body: formattedFormData
    });

	const data = await response.text(); // pokud PHPčko echuje, či print_r()uje text
	const data = await response.json(); // pokud PHPčko vrací JSON, echo json_encode()

	/* nějak zprocesovat data */
}
```
Ke kódu: [FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData/Using_FormData_Objects) - k posílání párů key/value přes XMLHttpRequest
```js
// lze vytvářet i takhle
const formattedFormData = {
	username: this.username.value,
	favorite_number: this.favorite_number.value
}
```

V PHPčku pak odchytávám proměnné jako klasicky `$_POST['name_of_the_input']`
