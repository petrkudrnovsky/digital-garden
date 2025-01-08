Odkaz na dokumentaci: https://github.com/FriendsOfPHP/Goutte
Alternativa: [hQuery](https://github.com/duzun/hQuery.php)
Výslovnost: \[gůt\]

### Akce pro všechny selectnuté elementy

```php
use Goutte\Client;  
use Symfony\Component\DomCrawler\Crawler;

$crawler->filter('h2.b-post__title > a')->each(function (Crawler $node) {
	$node->text();
	$node->links();
	$node->nodeName();
	// ...
}
```

### Získání odkazů

```php
$links = $crawler->filter('.productBox a')->links();
```

### Práce s formuláři
https://symfony.com/doc/current/components/dom_crawler.html#forms
- Goutte je tenký obal pro tuto komponentu a pro další (viz dokumentace)

### [DomCrawler](https://symfony.com/doc/current/components/dom_crawler.html) - komponenta, kterou Goutte využívá k procházení DOM

### Selectování
- lze pomocí [CSS selectors ](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors) nebo pomocí [XPathu](https://developer.mozilla.org/en-US/docs/Web/XPath) 
	- jejich rozdíly najdu [zde](https://gist.github.com/slotix/11f0930b2d46d2946249a10e6216735b)
	- XPath lze v DOMu spouštět přes [document.evaluate()](https://developer.mozilla.org/en-US/docs/Web/API/Document/evaluate)
