`wget [OPTIONS] URL`
- hlavně pro stahování souborů, ověřování certifikátů, rekurzivní stahování webů atd.

```bash
# -O (output-document) -stáhne soubor a uloží ho podle spec. názvu
wget -O mojsoubor.txt http://example.com/file.txt
# -r (recursive) - rekurzivní stahování webu (vč. stránek, na které odkazuje)
wget -r http://example.com/
# -l (level) - omezení hloubky stahování
wget -r -l 1 http://example.com/
# --spider -kontrola existence souborů, aniž by je stahoval
```

### Využití
```bash
# soubor je na jiné adrese s přesměrováním, takhle ho bude sledovat
wget -L http://example.com/redirect
# stažení webu i s CSS a JS (aby se dala zobrazit offline)
wget -p http://example.com
# komplet rekurzivní stažení webu
wget -r -p -k http://example.com
```