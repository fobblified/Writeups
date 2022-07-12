# Reflected XSS into a JavaScript string with angle brackets HTML encoded

## Theory

<h3>Выход из строки JavaScript</h3>

В тех случаях, когда контекст XSS находится внутри строкового литерала в кавычках, часто можно выйти за пределы строки и выполнить JavaScript напрямую. Крайне важно восстановить скрипт в соответствии с контекстом XSS, потому что любые синтаксические ошибки не позволят выполнить весь скрипт.

Некоторые полезные способы выхода из строкового литерала:
```
'-alert(document.domain)-'
';alert(document.domain)//
```

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_a_JavaScript_string_with_angle_brackets_HTML_encoded/assets/1.png)

Отправим запрос в строке поиска и перехватим его.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_a_JavaScript_string_with_angle_brackets_HTML_encoded/assets/2.png)

Для решения будем использовать следующий эксплоит:
```
'-alert(1)-'
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_a_JavaScript_string_with_angle_brackets_HTML_encoded/assets/3.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_a_JavaScript_string_with_angle_brackets_HTML_encoded/assets/4.png)
