# Web shell upload via Content-Type restriction bypass

## Theory

<h3>Неверная проверка типа файла</h3>

При отправке HTML-форм браузер обычно отправляет предоставленные данные в запросе POST с типом содержимого application/x-www-form-url-encoded. Это подходит для отправки простого текста, такого как ваше имя, адрес и т. д., но не подходит для отправки больших объемов двоичных данных, таких как весь файл изображения или документ PDF. В этом случае тип содержимого multipart/form-data является предпочтительным подходом.

Рассмотрим форму, содержащую поля для загрузки изображения, предоставления его описания и ввода вашего имени пользователя. Отправка такой формы может привести к запросу, который выглядит примерно так:
```
POST /images HTTP/1.1
Host: normal-website.com
Content-Length: 12345
Content-Type: multipart/form-data; boundary=---------------------------012345678901234567890123456

---------------------------012345678901234567890123456
Content-Disposition: form-data; name="image"; filename="example.jpg"
Content-Type: image/jpeg

[...binary content of example.jpg...]

---------------------------012345678901234567890123456
Content-Disposition: form-data; name="description"

This is an interesting description of my image.

---------------------------012345678901234567890123456
Content-Disposition: form-data; name="username"

wiener
---------------------------012345678901234567890123456--
```

Тело сообщения разбито на отдельные части для каждого из входных данных формы. Каждая часть содержит заголовок Content-Disposition, который предоставляет некоторую базовую информацию о поле ввода, к которому он относится. Эти отдельные части могут также содержать собственный заголовок Content-Type, сообщающий серверу MIME-тип данных, отправленных с использованием этого ввода.

Один из способов, которым веб-сайты могут попытаться проверить загрузку файлов, — это проверить, соответствует ли этот заголовок Content-Type для ввода ожидаемому типу MIME. Например, если сервер ожидает только файлы изображений, он может разрешать только такие типы, как image/jpeg и image/png. Проблемы могут возникнуть, когда сервер неявно доверяет значению этого заголовка. Если дальнейшая проверка не выполняется, чтобы проверить, действительно ли содержимое файла соответствует предполагаемому типу MIME, эту защиту можно легко обойти с помощью таких инструментов, как Burp Repeater.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_Content-Type_restriction_bypass/assets/1.png)

Логинимся c имеющимися данными. У нас есть возможность загрузки аватара пользователя. Загружаем эксплоит. Получаем сообщение том, что данный тип файла загрузить нельзя.

Код эксплоита:
```
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_Content-Type_restriction_bypass/assets/2.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_Content-Type_restriction_bypass/assets/3.png)

Найдем POST запрос /my-account/avatar. Отправляем его в Repeater.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_Content-Type_restriction_bypass/assets/4.png)

Изменим значение заголовка Content-Type на image/jpeg. Отправляем запрос, получаем сообщение о том, что файл успешно загружен.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_Content-Type_restriction_bypass/assets/5.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_Content-Type_restriction_bypass/assets/6.png)

Обновляем страницу. Теперь мы можем найти наш запрос с содержимым файла.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_Content-Type_restriction_bypass/assets/7.png)

Подтверждаем ответ и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_Content-Type_restriction_bypass/assets/8.png)
