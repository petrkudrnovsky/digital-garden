`nc [OPTIONS] [HOST] [PORT]`
- použití - jednoduché TCP/UDP spojení, ladění portů, přenos dat, nastavování serveru

```bash
# -l (listen) -připojení a naslouchání na daném portu
nc -l 8080
# -v (verbose) -podrobný výstup o připojování a příchozích zprávách
nc -v -l 8080
# -z (zero) -pošle nulový paket, aby se ověřil otevřený port
# ověření otevřeného portu 80 (TCP scan)
nc -z -v localhost 80
```

### Využití
```bash
# port scanning - vyzkoušení, které porty jsou otevřené
nc -z -v localhost 1-1000
# uložení serverové komunikace do souboru
nc -l 12345 > prijaty_soubor.txt
# poslání souboru klientovi přes port
nc IP_ADRESA_SERVERU 12345 < soubor.txt
```