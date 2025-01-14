Soubor, který se umisťuje do adresářů umístněných na webu - klienti k němu mají přístup a mohou ho upravovat. Tedy umožníme klientům spravovat určitou část konfigurace a pak pro změnu konfigurace pak nemusím restartovat celý server.

- konfigurace v tomto souboru je platná pro aktuální adresář a podadresáře
- mohu používat stejné direktivy použitelné v [[Direktivy pro Apache httpd#Kontejnery pro direktivy|kontejneru]] (např. `<Directory>`)

> [!warning] Vyhodnocování obsahu
> Tím, že nemusíme při změně konfigurace v .htaccess souboru restartovat server, tak pak se konfigurace musí vyhodnocovat při každém požadavku do adresáře, což trochu zpomaluje.

[[Direktivy pro Apache httpd|Direktiva]] `<AllowOverride>` povoluje a zakazuje možnost přepsání konfigurace konfigurací z `.htaccess` souboru

