Dokumentace: https://fosrestbundle.readthedocs.io/en/3.x/#getting-started-with-fosrestbundle
***
### Nastavení `config/packages/for_res.yaml`
```yaml
# Read the documentation: https://fosrestbundle.readthedocs.io/en/3.x/  
fos_rest:  
#    param_fetcher_listener:  true  
#    allowed_methods_listener:  true  
    routing_loader: false  
#    view:  
#        view_response_listener:  true  
#    exception:  
#        codes:  
#            App\Exception\MyException: 403  
#        messages:  
#            App\Exception\MyException: Forbidden area.  
    format_listener:  
        rules:  
            - { path: ^/api, prefer_extension: true, fallback_format: json, priorities: [ json, html ] }  
            - { path: '^/', priorities: [ 'html', '*/*'], fallback_format: ~, prefer_extension: true }
```
- důležité jsou ty `rules`, nastavuju, že na /api/^ bude chodit primárně JSON a na /^ bude normálně chodit HTMLko z Controllerů

Důležité je také nastavit to, že Api Controllery sedí jinde (v routes.yaml):
```yaml
controllers:  
	resource:  
		path: ../src/Controller/  
		namespace: App\Controller  
	type: attribute  
  
api:  
	resource: ../src/Api/Controller/  
	prefix: /api  
	type: attribute
```