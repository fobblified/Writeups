# Reflected XSS into HTML context with all tags blocked except custom ones

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_HTML_context_with_all_tags_blocked_except_custom_ones/assets/1.png)

Перейдем на exploit server.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_HTML_context_with_all_tags_blocked_except_custom_ones/assets/2.png)

В поле body поместим эксплоит. Сохраняем эксплоит и отправляем жертве. Код эксплоита:
```
<script>
location = 'https://0a7500ee03c9c24bc0ac1e4500dc0067.web-security-academy.net/?search=%3Cxss+id%3Dx+onfocus%3Dalert%28document.cookie%29%20tabindex=1%3E#x';
</script>
```

Код Экплоита без URL кодировки:
```
<script>
location = 'https://0a7500ee03c9c24bc0ac1e4500dc0067.web-security-academy.net/?search=<xss+id=x+onfocus=alert(document.cookie) tabindex=1>#x';
</script>
```

Переменная **location** внутри тега script в основном перенаправляет вас на веб-сайт, которому она назначена.

**<xss** - эта часть кода в основном создает настраиваемый тег с именем «xss». В данном случае это самозакрывающийся тег.

**id=x** - эта часть нужна для добавления атрибута id с именем «x» в настраиваемый тег \<xss>.

**onfocus=alert(document.cookie) onfocus** — это атрибут события, в котором он будет выполнять код javascript, когда элемент HTML, которому он назначен, получает фокус. Код **javascript:alert(document.cookie)** будет выполнен, как только произойдет событие.

**alert(document.cookie)** - всплывающее предупреждение, показывающее файл cookie на конкретном веб-сайте.

**tabindex=1** - атрибут tabindex в основном просто указывает порядок табуляции HTML-элемента, когда вы используете кнопку «Tab» для навигации.

**#x** - когда вы вводите этот код в строку браузера, он фокусируется на html-элементе с идентификатором x, в этом случае обрезанному xss был присвоен идентификатор x.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_HTML_context_with_all_tags_blocked_except_custom_ones/assets/3.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_HTML_context_with_all_tags_blocked_except_custom_ones/assets/4.png)
