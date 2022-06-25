# Web shell upload via path traversal

## Theory

<h3>Предотвращение выполнения файлов в доступных пользователю каталогах</h3>

Хотя в первую очередь лучше предотвратить загрузку опасных типов файлов, второй линией защиты является предотвращение выполнения сервером любых сценариев, которые проскальзывают через сеть.

В качестве меры предосторожности серверы обычно запускают только те сценарии, тип MIME которых они были явно настроены для выполнения. В противном случае они могут просто вернуть какое-то сообщение об ошибке или, в некоторых случаях, предоставить содержимое файла в виде обычного текста:
```
GET /static/exploit.php?command=id HTTP/1.1
Host: normal-website.com


HTTP/1.1 200 OK
Content-Type: text/plain
Content-Length: 39

<?php echo system($_GET['command']); ?>
```

Такое поведение потенциально интересно само по себе, так как может привести к утечке исходного кода, но сводит на нет любую попытку создать веб-оболочку.

Такая конфигурация часто отличается в разных каталогах. Каталог, в который загружаются пользовательские файлы, вероятно, будет иметь гораздо более строгий контроль, чем другие места в файловой системе, которые считаются недоступными для конечных пользователей. Если вы сможете найти способ загрузить сценарий в другой каталог, который не должен содержать файлов, предоставленных пользователем, сервер все же может выполнить ваш сценарий.

Вы также должны отметить, что даже если вы можете отправлять все свои запросы на одно и то же доменное имя, это часто указывает на какой-либо обратный прокси-сервер, например, на балансировщик нагрузки. Ваши запросы часто будут обрабатываться дополнительными серверами за кулисами, которые также могут быть настроены по-другому.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_path_traversal/assets/1.png)

Логинимся c имеющимися данными. У нас есть возможность загрузки аватара пользователя. Загружаем эксплоит. Получаем сообщение том, что файл был успешно загружен.

Код эксплоита:
```
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_path_traversal/assets/2.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_path_traversal/assets/3.png)

Найдем GET запрос /files/avatar/exploit.php. Мы можем заметить, что мы получаем сожержимое файла, значит экплоит не обрабатывается.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_path_traversal/assets/4.png)

Модифицируем запрос отправки файла, указав в его названии его расположение. Отправляем запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_path_traversal/assets/5.png)

Обновляем страницу. Теперь мы можем найти наш запрос с содержимым файла.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_path_traversal/assets/6.png)

Подтверждаем ответ и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_path_traversal/assets/7.png)
