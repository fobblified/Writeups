# Reflected XSS with some SVG markup allowed

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_with_some_SVG_markup_allowed/assets/1.png)

В строке поиска введем случайное значение, отправим запрос и перехватим его.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_with_some_SVG_markup_allowed/assets/2.png)

Отправляем запрос в Intruder. На данном этапе нам необходимо найти разрешенные тэги. Помести специальные символы между <>. 

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_with_some_SVG_markup_allowed/assets/3.png)

Воспользуемся [Portswigger XSS cheat sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet) для того чтобы скопировать все тэги.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_with_some_SVG_markup_allowed/assets/4.png)

Помещаем тэги в меню payloads.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_with_some_SVG_markup_allowed/assets/5.png)

Запускаем атаку. Мы видим, что у нас имеется несколько тэгов которые мы можем использовать. 

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_with_some_SVG_markup_allowed/assets/6.png)

Попробуем найти разрешенные атрибуты тэга animatetransform. Проделаем те же шаги.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_with_some_SVG_markup_allowed/assets/7.png)

Скопируем на сайте атрибуты и вставим в меню payloads.Запускаем атаку. У нас имеется атрибут onbegin, который мы можем использовать.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_with_some_SVG_markup_allowed/assets/8.png)

Вместе с этим нам необходимо использовать тэг svg. Вставляем эксплоит в строку поиска. Код эсплоита выглядит слеующим образом:
```
<svg><animatetransform onbegin=alert("1")>
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_with_some_SVG_markup_allowed/assets/9.png)

Мы можем видеть работу эксплоита.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_with_some_SVG_markup_allowed/assets/10.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_with_some_SVG_markup_allowed/assets/11.png)
