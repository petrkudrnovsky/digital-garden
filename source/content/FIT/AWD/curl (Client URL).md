`curl [OPTIONS] URL`
- podporuje HTTP, HTTPS, FTP atd.
- využití: testování API, stahování souborů z internetu, odesílání GET/POST požadavků
```bash
curl http://localhost:80
# -X (request) -určení metody, implicitně je to GET
curl -X DELETE http://localhost/resource
# -d (data) -data v rámci POST, jdou i JSON data (viz druhý příklad)
curl -d "username=john&password=secret" http://localhost/login
curl -X POST -H "Content-Type: application/json" -d '{"key1":"value1", "key2":"value2"}' http://localhost/api
# - H (header)
curl -H "Content-Type: application/json" http://localhost/resource
# -O (remote-name) -stáhne soubor pod stejným názvem
curl -O http://example.com/file.zip
# -L (location) sleduje přesměrování 3xx a automaticky pokračuje na novou adresu
curl -L http://localhost/old-url
# -I (head) získá pouze hlavičky, nestahuje body
curl -I http://localhost/resource
# -v (verbose) -ukecaný, veškerá komunikace se serverem, která proběhne
curl -v http://localhost/resource
```