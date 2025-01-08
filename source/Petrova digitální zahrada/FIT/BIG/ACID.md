### Atomicity
Transakce musí vždy proběhnout celá nebo vůbec
### Consistency
Transakce transformuje databázi z jednoho konzistentního stavu do jiného konzistentního stavu
### Independence
Dílčí efekty jedné transakce nejsou viditelné ostatním transakcím (tj. nevidí si pod ruce, je to zamčené)
### Durability
Pokud transakce doběhne úspěšně, tak jsou její změny perzistentně uložené

### ACID vs. [[BASE model]]

|**Vlastnost**|**BASE**|**ACID**|
|---|---|---|
|**Dostupnost**|Vysoká, i za cenu nekonzistence dat|Nižší, závisí na udržení konzistence|
|**Konzistence**|Eventual Consistency (časem konzistentní)|Přísná konzistence|
|**Škálovatelnost**|Snadno škálovatelné horizontálně|Obtížnější horizontální škálování|
|**Vhodné aplikace**|Big Data, real-time aplikace|Bankovní systémy, finanční transakce|
|**Odolnost vůči výpadkům**|Toleruje nekonzistenci, udržuje dostupnost|Selhání může narušit konzistenci|
