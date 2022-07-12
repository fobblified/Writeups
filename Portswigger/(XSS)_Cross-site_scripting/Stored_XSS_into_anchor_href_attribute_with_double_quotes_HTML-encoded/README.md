# Stored XSS into anchor href attribute with double quotes HTML-encoded

## Theory

<h3>XSS в атрибутах HTML-тегов</h3>

Когда контекст XSS находится в значении атрибута HTML-тега, иногда вы можете завершить значение атрибута, закрыть тег и ввести новый. Например:
```
"><script>alert(document.domain)</script>
```

Иногда контекст XSS относится к типу атрибута HTML-тега, который сам по себе может создавать контекст, доступный для сценариев. Здесь вы можете выполнить JavaScript без необходимости завершать значение атрибута. Например, если контекст XSS находится в атрибуте href тега привязки, вы можете использовать псевдопротокол javascript для выполнения скрипта.Например:
```
<a href="javascript:alert(document.domain)">
```

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Stored_XSS_into_anchor_href_attribute_with_double_quotes_HTML-encoded/assets/1.png)

Перейдем к любому посту. Спустимся в низ страницы. У нас есть поле для комментария.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Stored_XSS_into_anchor_href_attribute_with_double_quotes_HTML-encoded/assets/2.png)

Вставим наш экплоит в поле Website и отправим запрос. Код экплоита:
```
javascript:alert(1)
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Stored_XSS_into_anchor_href_attribute_with_double_quotes_HTML-encoded/assets/3.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Stored_XSS_into_anchor_href_attribute_with_double_quotes_HTML-encoded/assets/4.png)
