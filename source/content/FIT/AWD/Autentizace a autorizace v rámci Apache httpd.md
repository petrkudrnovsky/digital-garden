Obecně [[Autorizace]] a [[Autentizace]].
## Autentizace
- rozhodnutí, jestli má klient vůbec přístup do systému
- *realm* - oblast, kam se přihlašuju
	- pro případ, kdy chci mít více nezávislých oblastí, kam chci být autentizován (v každé za jiného uživatele/jiným způsobem), tak se dají rozlišovat realmem
	- `AuthName` direktiva uvádí jméno oblasti/realmu (to se pak posílá v odpovědi)
	- `AuthType` direktiva určuje typ autentizace
- autentizace se většinou nastavuje v nějakém kontextu ([[Direktivy pro Apache httpd#Kontejnery pro direktivy|kontejneru]])

Máme 2 hlavní metody autentizace:
#### [[Autentizace#HTTP Basic ([MDN](https //developer.mozilla.org/en-US/docs/Web/HTTP/Authentication))|HTTP Basic]]
#### [[Autentizace#HTTP Digest|HTTP Digest]]

> [!tip] Dnešní praktiky
> Dneska se vlastně používá jednoduchá [[#Autentizace HTTP Basic ([MDN](https //developer.mozilla.org/en-US/docs/Web/HTTP/Authentication)) HTTP Basic|HTTP Basic]] autentizace a bezpečnost se řeší šifrováním toho spojení - SSL/TLS (HTTPS)
#### Uložení uživatelů
- v souboru `.htpasswd` jsou jednotliví uživatelé
	- ten je obsluhován příkazem `htpasswd`
- v souboru `.htgroup` jsou pak jednotlivé skupiny
***
## Autorizace
- hlavní direktiva `Require`, používá se také v kontextu ([[Direktivy pro Apache httpd#Kontejnery pro direktivy|kontejneru]]) - řídí přístup ke zdroji podle různých kritérií
- pokud požadovaný zdroj neexistuje, tak je přístup odepřen

```
<Directory /var/www/main/private>
  AuthType basic
  AuthName "Secured"
  AuthBasicProvider file
  AuthBasicAuthoritative On
  AuthUserFile /var/www/.htpasswd
  Require valid-user
</Directory>
```

`Require group alpha` - pouze uživatelé skupiny alpha
- `AuthMerging` - jak nakládat s různými množinami uživatelů (přístupová práva se dědí)