*Extensible Hyper Text Markup Language*

Je to "přísnější" verze [[HTML]], je to HTML postavené na [[XML]] pravidlech. Měla sloužit k tomu, že jak pak byla syntakticky správně, tak pak se s tím dál strojově lépe pracovalo.

> [!warning] Nepoužívá se
> Už se nepoužívá, nahradilo ho [[HTML#HTML5|HTML 5]], které umožňuje XML-like syntax a je flexibilnější 
### Rozdíly
- všechny tagy musí být uzavíratelné (nelze `<img>`, ale `<img />`)
- všude musí být psáno malým písmem (je to doporučené, ne vyžadované)
- ==prostě to bylo syntakticky přísnější== 

Musel se uvádět [[MIME type]] (aby XML bylo validní): `application/xhtml+xml`

Výhodou bylo, že to bylo XML, takže se na to daly uplatňovat pravidla pro parsování XML či transformace pomocí [[XSLT]].