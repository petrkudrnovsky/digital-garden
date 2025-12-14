# 1. tutorial - Introduction
- [[curl (Client URL)]]
- NodeJS is an Event-driven I/O framework
	- is single-threaded, has an Event loop 
# 2. tutorial - Communication protocols
- security (SSL/TLS) works on the Session layer (= relační vrstva) of the [[ISO OSI model]] (it's the [[TCP IP model]] middle-layer)
	- there is a security "handshake" after the TCP handshake
	- with ClientHello and ServerHello + certificate (and it's verification) to ensure the HTTPS connection
		- the process is here [[Certifikát a certifikační autorita#Jak to funguje?]]
		- [[Bezpečnost webového serveru#Protokol SSL/TLS]]
- [[HTTP protokol#HTTP/2]]
- [[AM1 - 4. lecture#TLS + proxy servers and their modes]]
- [[AM1 - 4. lecture#SNI = modern solution to virtual hosting]]
# 3. tutorial - REST and HATEOAS
- resource has URL (identifier) and (multiple) representations
- hypertext is a representation of a resource with links (URIs of resources)
- when handling asynchronous operations, the server should send back 202 Accepted with the statusUrl, where the client can check the status of the task processing
# 4. tutorial - gRPC
- [[RPC protokol]]
# 5. tutorial - Messaging systems
# 6. tutorial - Load balancers