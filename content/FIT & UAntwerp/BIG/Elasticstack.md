Jedná se o sadu nástrojů:
- [[Elasticsearch]]
- [[Logstash]]
- Kibana
	- vizualizace dat, detailní analýza dat v reálném čase
	- full-textové vyhledávání
	- je plně závislá na [[Elasticsearch]], nejde použít samostatně
- Beats
	- lehké nástroje pro sběr specifických typů dat
		- sbírá data a posílá na server
	- aby nebylo nutné na každý koncový bod instalovat [[Logstash]], který je náročný na výkon
	- například Filebeat

Díky Elasticstacku můžeme analyzovat real-time velké objemy dat. Používá se v **monitoringu** aplikací, **analýze logů** nebo **vyhledávání v dokumentech.**

