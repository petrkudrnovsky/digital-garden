```mermaid
flowchart TD
    subgraph INTERNET
        CLIENT["CLIENT(LIB)"]
        QUERIES["SQL / Cypher / XQuery / ..."]
    end

    subgraph INSTANCE_CLUSTER["INSTANCE / CLUSTER"]
        PORT["PORT / TCP-IP"]
        subgraph MEMORY["Memory Structures"]
            DBBUF["DB Buffer"]
            JOURNAL["Journal"]
            SRVMEM["Server Process Memory"]
            MORE["..."]
        end
        subgraph PROCESSES["Processes"]
            P1(( )) 
            P2(( ))
            P3(( ))
            P4(( ))
            P5(( ))
            P6(( ))
        end
    end

    subgraph DATABASE["DATABASE"]
        STORAGE["Backup | Data | Metadata | Statistics | Journal Log | Indexes | Temp | Config"]
    end

    CLIENT -- "sends queries via" --> PORT
    QUERIES --> PORT
    PORT --> SRVMEM
    INSTANCE_CLUSTER --> DATABASE
```
- na DB serveru (fyzický/virtuální) běží Linux/UNIX a tam je nainstalovaný databázový server
	- občas se ten OS musí nakonfigurovat (např. pro Oracle se musí zvětšit nějaké paramentry, protože má větší nároky)
- komunikuje se hlavně přes TCP/IP (jiné možnosti jsou, ale moc se nepoužívají)
- poslouchá se na nějakém portu
	- PostgreSQL: 5432
	- na portu proběhne autentizace/autorizace
		- pak dostanu samostatný proces, který si se mnou (jako s klientem) povídá
- 2 červené vrstvy (v obrázku)
	- instance/cluster
		- zajištuje připojování klientů a komunikaci s databázovou částí
		- Oracle - pojem instance
		- [[PostgreSQL]] - pojem cluster
			- cluster je tady sdílená paměť a různé procesy kolem
			- není to cluster v pravým slova smyslu
	- databáze
		- je perzistentní
		- nejde přistoupit přímo, musím nastartovat instanci/cluster a pomocí toho přistupovat
	- 80 % je to 1:1, může být více instancí/clusterů na 1 databázi, ale zas tak běžný to není
- se serverem se komunikuje pomocí API
	- 90 % je to SQL
	- další možnosti jsou CYPHER (např. pro [[Neo4j]])
	- XQUERY - pro XML-based databáze (to jsou např. [[Dokumentově orientovaná databáze]])