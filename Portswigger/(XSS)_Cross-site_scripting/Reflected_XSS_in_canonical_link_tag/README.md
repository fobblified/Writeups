# Reflected XSS in canonical link tag

## Theory

<h3>XSS в атрибутах HTML-тегов</h3>

Вы можете столкнуться с веб-сайтами, которые кодируют угловые скобки, но при этом позволяют вводить атрибуты. Иногда эти инъекции возможны даже внутри тегов, которые обычно не запускают события автоматически, например канонического тега. Вы можете использовать это поведение, используя ключи доступа и взаимодействие с пользователем в Chrome. Клавиши доступа позволяют вам предоставлять сочетания клавиш, которые ссылаются на определенный элемент. Атрибут accesskey позволяет определить букву, нажатие которой в сочетании с другими клавишами (они различаются на разных платформах) вызовут срабатывание событий.

## Writeup

Для решения данной лабораторной работы необходимо использовать Chrome.

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_in_canonical_link_tag/assets/1.png)

Для решения будем использовать следующий экплоит:
```
https://0a8f004603320401c05b686f004100b3.web-security-academy.net/?%27accesskey=%27x%27onclick=%27alert(1)
```

Код экплоита без URL кодировки:
```
https://0a8f004603320401c05b686f004100b3.web-security-academy.net/?'accesskey='x'onclick='alert(1)
```

Для вызова alert-а необходимо использовать одну из комбинаций клавиш(комбинация зависит от операционой системы, которую вы используете)

* ALT+SHIFT+X - на Windows
* CTRL+ALT+X - на MacOS
* Alt+X - на Linux

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_in_canonical_link_tag/assets/2.png)
