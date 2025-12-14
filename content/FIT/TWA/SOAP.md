- = Simple Object Access Protocol
- posílání strukturovaných zpráv je pomocí [[XML]] přes [[HTTP protokol]] (či jiné protokoly (SMTP, JMS))
- zajišťuje komunikaci mezi komponentami systému
- hodně balastu kolem, je to hodně strikně typované (není to kvůli tomu tolik oblíbené)
	- díky tomu oblíbené ve státní správě a bankovnictví -> je to spolehlivé
	- ale v dnešní době se spíše využívá REST API a JSON, pro jejich jednoduchost, větší rychlost atd.
### Složení SOAP zprávy
- zprávy chodí v obálkách (= Envelope), což je takový kontejner pro zprávu
- v obálce jsou:
	- Header
		- obsahuje metadata (routing informace, bezpečnostní prvky, kontext k posílání atd.)
	- Body (payload)
		- obsah zprávy v XML
	- Attachment
		- binární data, která nemohla být serializována do XML
### Příklady:
Request:
```xml
POST /InvoiceService HTTP/1.1
Host: example.com
Content-Type: text/xml

<?xml version="1.0"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <GetInvoice xmlns="http://example.com/invoice">
      <InvoiceId>12345</InvoiceId>
    </GetInvoice>
  </soap:Body>
</soap:Envelope>
```

Response:
```xml
<?xml version="1.0"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <GetInvoiceResponse xmlns="http://example.com/invoice">
      <Invoice>
        <Id>12345</Id>
        <Amount>1500.00</Amount>
        <Currency>CZK</Currency>
      </Invoice>
    </GetInvoiceResponse>
  </soap:Body>
</soap:Envelope>
```
### WSDL
- = Web Services Description Language
- XML dokument, který popisuje SOAP službu (podobně jako API specifikace)
	- má podobné součásti jako [[Rozhraní (Interface)#Části interface]]
		- tj. definice datových typů, operace (a jejich input/output objekty), formáty, specifikace URL endpointu pro konkrétní službu
- existují různé nástroje na generování tříd a služeb pro komunikaci přes SOAP podle formátu ve WSDL (je to machine-readable XMLko)