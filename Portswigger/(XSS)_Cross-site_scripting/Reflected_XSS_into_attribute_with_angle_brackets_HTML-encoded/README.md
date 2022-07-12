# Reflected XSS into attribute with angle brackets HTML-encoded

## Theory

<h3>XSS в атрибутах HTML-тегов</h3>

Когда контекст XSS находится в значении атрибута HTML-тега, иногда вы можете завершить значение атрибута, закрыть тег и ввести новый. Например:
```
"><script>alert(document.domain)</script>
```

Чаще всего в этой ситуации угловые скобки заблокированы или закодированы, поэтому ваш ввод не может выйти за пределы тега, в котором он появляется. При условии, что вы можете завершить значение атрибута, вы обычно можете ввести новый атрибут, который создает контекст для сценариев, например обработчик событий. Например:
```
" autofocus onfocus=alert(document.domain) x="
```

Вышеупомянутая полезная нагрузка создает событие onfocus, которое будет выполнять JavaScript, когда элемент получает фокус, а также добавляет атрибут autofocus, чтобы попытаться вызвать событие onfocus автоматически без какого-либо взаимодействия с пользователем. Наконец, он добавляет x=', чтобы исправить следующую разметку.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_attribute_with_angle_brackets_HTML-encoded/assets/1.png)

Отправим запрос в строке поиска и перехватим его.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_attribute_with_angle_brackets_HTML-encoded/assets/2.png)

Для решения будем использовать следующий эксплоит:
```
"onmouseover="alert(1)
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_attribute_with_angle_brackets_HTML-encoded/assets/3.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_attribute_with_angle_brackets_HTML-encoded/assets/4.png)
