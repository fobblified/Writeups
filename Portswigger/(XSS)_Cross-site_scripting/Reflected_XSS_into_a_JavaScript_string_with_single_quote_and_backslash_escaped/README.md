# Reflected XSS into a JavaScript string with single quote and backslash escaped

## Theory

<h3>XSS в JavaScript</h3>

Когда контекст XSS представляет собой какой-то существующий JavaScript в ответе, может возникнуть множество ситуаций с различными методами, необходимыми для успешного выполнения эксплойта.

<h3>Завершение существующего скрипта</h3>

В простейшем случае можно просто закрыть тег скрипта, заключающий в себе существующий код JavaScript, и ввести несколько новых тегов HTML, которые будут запускать выполнение кода JavaScript. Например, если контекст XSS выглядит следующим образом:
```
<script>
...
var input = 'controllable data here';
...
</script>
```

Затем вы можете использовать следующую полезную нагрузку, чтобы выйти из существующего JavaScript и выполнить свой собственный:
```
</script><img src=1 onerror=alert(document.domain)>
```

Причина, по которой это работает, заключается в том, что браузер сначала выполняет синтаксический анализ HTML для идентификации элементов страницы, включая блоки сценариев, и только позже выполняет синтаксический анализ JavaScript для понимания и выполнения встроенных сценариев. Вышеупомянутая полезная нагрузка оставляет исходный сценарий сломанным с незавершенным строковым литералом. Но это не мешает анализу и выполнению последующего скрипта в обычном режиме.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_a_JavaScript_string_with_single_quote_and_backslash_escaped/assets/1.png)

Отправим запрос в строке поиска и перехватим его.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_a_JavaScript_string_with_single_quote_and_backslash_escaped/assets/2.png)

Для решения будем использовать следующий эксплоит:
```
</script></script>alert(1)</script>
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_a_JavaScript_string_with_single_quote_and_backslash_escaped/assets/3.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_a_JavaScript_string_with_single_quote_and_backslash_escaped/assets/4.png)
