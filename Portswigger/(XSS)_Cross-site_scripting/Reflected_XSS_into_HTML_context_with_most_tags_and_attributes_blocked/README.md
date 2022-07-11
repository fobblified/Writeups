# Reflected XSS into HTML context with most tags and attributes blocked

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_HTML_context_with_most_tags_and_attributes_blocked/assets/1.png)

В строке поиска попробуем отправить стандартный скрипт для проверки на XSS. Код:
```
<img src/onerror=alert(1)>
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_HTML_context_with_most_tags_and_attributes_blocked/assets/2.png)

Мы получаем ошибку, т.к. приложение имеет фильтрацию тэгов и атрибутов. Отправим еще один запрос и перехватим его.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_HTML_context_with_most_tags_and_attributes_blocked/assets/3.png)

Отправляем запрос в Intruder. На данном этапе нам необходимо найти разрешенные тэги. Помести специальные символы между <>. 

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_HTML_context_with_most_tags_and_attributes_blocked/assets/4.png)

Воспользуемся [Portswigger XSS cheat sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet) для того чтобы скопировать все тэги.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_HTML_context_with_most_tags_and_attributes_blocked/assets/5.png)

Помещаем тэги в меню payloads.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_HTML_context_with_most_tags_and_attributes_blocked/assets/6.png)

Запускаем атаку. Мы видим, что у нас имеется тэг body, который мы можем использовать.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_HTML_context_with_most_tags_and_attributes_blocked/assets/7.png)

Теперь же мы должны найти разрешенные атрибуты. Проделаем те же шаги.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_HTML_context_with_most_tags_and_attributes_blocked/assets/8.png)

Скопируем на сайте атрибуты и вставим в меню payloads.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_HTML_context_with_most_tags_and_attributes_blocked/assets/9.png)

Запускаем атаку. У нас имеется атрибут onresize, который мы можем использовать.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_HTML_context_with_most_tags_and_attributes_blocked/assets/10.png)

Перейдем на exploit server.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_HTML_context_with_most_tags_and_attributes_blocked/assets/11.png)

В поле body поместим эксплоит. Сохраняем эксплоит и отправляем жертве. Код эксплоита:
```
<iframe src="https://0abf001f0332c2a5c03a18d700a2007d.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=print()%3E" onload=this.style.width='100px'>
```

Код Экплоита без URL кодировки:
```
<iframe src="https://0abf001f0332c2a5c03a18d700a2007d.web-security-academy.net/?search="><body onresize=print()>" onload=this.style.width='100px'>
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_HTML_context_with_most_tags_and_attributes_blocked/assets/12.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_HTML_context_with_most_tags_and_attributes_blocked/assets/13.png)
