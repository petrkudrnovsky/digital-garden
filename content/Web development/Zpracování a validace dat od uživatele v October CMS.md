
- Mám HTML formulář, data odešle a přijme AJAX a samotnou validaci (a další úkony) vykoná funkce v pluginu (PHPčko)
- Pro správné fungování AJAXu je ho potřeba na stránku přidat: `{% framework %}` - nejlépe na konec `<body>` tagu, kde se většinou nachází další skripty (aby se načítaly až potom, co se načte samotný obsah stránky)
	- ještě je možné includovat `{% framework extras %}`
		- zde se nachází další JavaScript skripty a stylování, které hlavně pomáhá front-end validaci formulářů 
		- ukazatel načítání, validace, validační výjimky, zobrazování errorů
		- více zde: https://docs.octobercms.com/2.x/ajax/extras.html

### Formulář
```html
<!-- specifikace funkce, která se zavolá v Pluginu (přidán přes {% component 'contactform' %} -->
<form data-request="onSend"  
      data-request-update="'form-message': '#message', 'form-message-name-error': '#nameError', 'form-message-email-error': '#emailError', 'form-message-content-error': '#contentError'"  
      data-request-redirect="/#kontakt"  
      
      data-request-complete="if(data.responseJSON['result'] === 'success'){document.getElementById('contactForm').reset()};"  
      class="form-wrap"  
      id="contactForm"  
>  
    <label for="contact-name">Jméno a příjmení:</label><br>  
    <input type="text" id="contact-name" name="name" required><br>  
    <div class="err-message">{{ errors.first('name') }}</div>  
    <div id="nameError">{% partial 'form-message-name-error' %}</div>  
  
    <label for="contact-email">E-mail:</label><br>  
    <input type="text" id="contact-email" name="email" required><br>  
    <div id="emailError">{% partial 'form-message-email-error' %}</div>  
  
    <label for="contact-textarea">Text zprávy:</label><br>  
    <textarea id="contact-textarea" name="content" required></textarea><br>  
    <div id="contentError">{% partial 'form-message-content-error' %}</div>  
  
    <button type="submit" class="form-submit-btn">Odeslat</button>  
    <span id="message">{% partial 'form-message' %}</span>  
</form>
```

### Pluginová funkce onSend()

```php
public function onSend()  
{  
    $messages = [  
        'name.required' => 'Vaše jméno není vyplněné.',  
        'email.required' => 'Váš e-mail není vyplněný.',  
        'email.email' => 'Váš e-mail není validní e-mail.',  
        'content.required' => 'Vaše zpráva je prázdná.',  
    ];  

	// jedná se o Octoberovský Validator -> use Validator;
    $validator = Validator::make(  
        [            
	        'name' => input('name'),  
            'email' => input('email'),  
            'content' => input('content'),  
        ],  
        [  
            'name' => 'required',  
            'email' => 'required|email',  
            'content' => 'required',  
        ],  
        $messages  
    );  
  
    if($validator->fails()) {  
        if(!empty($validator->errors()->get('name'))) {  
            $this->page['formMessageNameError'] = $validator->errors()->get('name')[0];  
        }  
  
        if(!empty($validator->errors()->get('email'))) {  
            $this->page['formMessageEmailError'] = $validator->errors()->get('email')[0];  
        }  
  
        if(!empty($validator->errors()->get('content'))) {  
            $this->page['formMessageContentError'] = $validator->errors()->get('content')[0];  
        }  
        // vrátí se jako objekt 'data', který nese spoustu informací ohledně Response z AJAXu, včetně responseJSON, kde je i tato dvojice (viz data-request-complete skript ve Formuláři výše)
        return [  
            'result' => 'error',  
        ];  
    }  
    else {  
        $vars = ['name' => input('name'), 'email' => input('email'), 'content' => input('content')];  
        $this->page['formMessage'] = "Děkujeme za zprávu, co nejdříve se Vám ozveme na Váš e-mail.";  
        Mail::send('externity.contact::mail.message', $vars, function($message) {  
            $message->to("test@email.com", 'Sender Name');  
        });  
        // posílám jen pro informaci, jak skončila validace, zpět na front-end
        return [  
            'result' => 'success',  
        ];  
    }  
}
```

### Partials
- partials, které používám na zobrazení error zpráv a potvrzovací zprávy jsou velmi jednoduché
- např. form-message-name-error.htm má obsah `{{ formMessageNameError }}`, stejně jako ostatní

### Zajímavé odkazy
[AJAX v Octoberu](https://docs.octobercms.com/2.x/ajax/introduction.html) - načítání dat ze serveru bez page refreshe
[Data Attributes API](https://docs.octobercms.com/2.x/ajax/attributes-api.html) - attributy (nejen k `<form>` elementu pro napojení na AJAX)
