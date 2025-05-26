Dokumentace: https://www.slimframework.com/docs/v4/

```php
// klasické includy - pozor jsou přejmenované
use Psr\Http\Message\ResponseInterface as Response;
use Psr\Http\Message\ServerRequestInterface as Request;
use Slim\Factory\AppFactory;

require __DIR__ . '/../vendor/autoload.php';

// vytvoření Slim aplikace
$app = AppFactory::create();

// definování routes, na kterých se něco děje
$app->get('/', function (Request $request, Response $response, $args) {
    $response->getBody()->write("Hello world!");
    return $response;
});

// spuštění aplikace
$app->run();
```

### Vracení JSON dat
```php
$data = ['name' => 'Bob', 'age' => 40];
$payload = json_encode($data);

$response->getBody()->write($payload);

// specifikování kódu odpovědi
$response = $response->withStatus(201); 
// potřeba hlavičkou říct, že vracím JSON
return $response->withHeader('Content-Type', 'application/json');
```

### Dostávání JSON dat
- je potřeba si předpřipravit aplikace pomocí Middleware

```php
$app->addBodyParsingMiddleware();

$app->post('/customers', static function (Request $request, Response $response) {  
    // ...
    $userPayload = $request->getParsedBody();
    // jedná se o vstup od uživatele, mělo by proběhnout nějaké ošetření
    $username = $userPayload['name'];
    $password = $userPayload['password'];
    //...
}
```
- v Postmanovi mohu JSON data zadat takhle (v raw formátu)
![[Pasted image 20221128152519.png]]

### Middleware
- kód, který běží před/po vykonáním kódu pro danou routu
- příprava na JSON vstup od uživatele (Middleware od Oldy Malece)

```php
namespace Books\Middleware;  
  
use Psr\Http\Message\ResponseInterface as Response;  
use Psr\Http\Message\ServerRequestInterface as Request;  
use Psr\Http\Server\MiddlewareInterface;  
use Psr\Http\Server\RequestHandlerInterface as RequestHandler;  
  
class JsonBodyParserMiddleware implements MiddlewareInterface  
{  
    public function process(Request $request, RequestHandler $handler): Response  
    {  
        $contentType = $request->getHeaderLine('Content-Type');  
  
        if (str_contains($contentType, 'application/json')) {  
            $contents = json_decode(file_get_contents('php://input'), true);  
            if (json_last_error() === JSON_ERROR_NONE) {  
                $request = $request->withParsedBody($contents);  
            }  
        }  
        return $handler->handle($request);  
    }  
}

// ... 
// použití

$app->post(...) {
	$payload = $request->getParsedBody();
}
```
- nebo `$app->addBodyParsingMiddleware();` viz výše

- autorizace přístupu k dané routě - můj Middleware
```php
namespace Books\Middleware;  
  
use Psr\Http\Message\ServerRequestInterface as Request;  
use Psr\Http\Message\ResponseInterface as Response;  
use Psr\Http\Server\MiddlewareInterface;  
use Psr\Http\Server\RequestHandlerInterface as RequestHandler;  
  
class AuthorizationMiddleware implements MiddlewareInterface  
{  
    public function process(Request $request, RequestHandler $handler): Response  
    {  
	    // nastavení vnitřního atributu
        $request = $request->withAttribute('approved', false);  
        $hashedLogin = $request->getHeader('Authorization');  
        if(!empty($hashedLogin)) {  
            $hashedLoginArr = explode(' ', $hashedLogin[0]);  
            if($hashedLoginArr[0] !== "Basic") {  
                return $handler->handle($request);  
            }  

			// druhá část hashe
            $hash = $hashedLoginArr[1]; 
            $data = base64_decode($hash);  
            if($data === false) {  
                return $handler->handle($request);  
            }  
            $credentials = explode(':', $data);  
            // hard-coded uživatel
            if($credentials[0] === 'admin' && $credentials[1] === 'pas$word') {  
                $request = $request->withAttribute('approved', true);  
            }  
        }  
        return $handler->handle($request);  
    }  
}
```
- pak chci mít v daném Requestu v hlavičce přidanou novou hodnotu (screen z Postmana):
![[Pasted image 20221128151530.png]]
- zde používám [Basic Auth](https://en.wikipedia.org/wiki/Base64) (wikipedia) a [(en|de)coder](https://www.base64decode.org/) 