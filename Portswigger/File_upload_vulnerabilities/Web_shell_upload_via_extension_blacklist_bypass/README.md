# Web shell upload via extension blacklist bypass

## Theory

<h3>Недостаточный черный список опасных типов файлов</h3>

Один из наиболее очевидных способов предотвратить загрузку пользователями вредоносных скриптов — внести в черный список потенциально опасные расширения файлов, такие как .php. Практика занесения в черный список изначально ошибочна, поскольку трудно явно заблокировать все возможные расширения файлов, которые могут использоваться для выполнения кода. Такие черные списки иногда можно обойти, используя менее известные альтернативные расширения файлов, которые все еще могут быть исполняемыми, такие как .php5, .shtml и т. д.

<h3>Переопределение конфигурации сервера</h3>

Как мы обсуждали в предыдущем разделе, серверы обычно не будут выполнять файлы, если они не были настроены для этого. Например, прежде чем сервер Apache выполнит файлы PHP, запрошенные клиентом, разработчикам может потребоваться добавить следующие директивы в свой файл /etc/apache2/apache2.conf:
```
LoadModule php_module /usr/lib/apache2/modules/libphp.so
AddType application/x-httpd-php .php
```

Многие серверы также позволяют разработчикам создавать специальные файлы конфигурации в отдельных каталогах, чтобы переопределить или добавить один или несколько глобальных параметров. Серверы Apache, например, будут загружать конфигурацию для конкретного каталога из файла с именем .htaccess, если он присутствует.

Точно так же разработчики могут настраивать конкретные каталоги на серверах IIS с помощью файла web.config. Сюда могут входить такие директивы, как следующие, которые в данном случае позволяют предоставлять пользователям файлы JSON:
```
<staticContent>
    <mimeMap fileExtension=".json" mimeType="application/json" />
</staticContent>
```

Веб-серверы используют файлы конфигурации такого типа, если они есть, но обычно вам не разрешен доступ к ним с помощью HTTP-запросов. Однако иногда вы можете обнаружить серверы, которые не могут помешать вам загрузить собственный вредоносный файл конфигурации. В этом случае, даже если нужное вам расширение файла занесено в черный список, вы можете обманным путем заставить сервер сопоставить произвольное пользовательское расширение файла с исполняемым типом MIME.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_extension_blacklist_bypass/assets/1.png)

Логинимся c имеющимися данными. У нас есть возможность загрузки аватара пользователя. Загрузим любую картинку и посмотрим, что содержит отправляемый запрос. Просмотрев запрос, можно обнаружить, что в запросе также находится содержимое нашего файла.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_extension_blacklist_bypass/assets/2.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_extension_blacklist_bypass/assets/3.png)

Теперь же загрузим наш эксплоит. Найдем запрос. Мы видим ошибку, файлы с расширением .php недоступны для загрузки.

Код эксплоита:
```
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_extension_blacklist_bypass/assets/4.png)

Отправим этот запрос в Repeater. В ответе к запросы, мы можем видеть, что сервером является Apache. Мы можем изменить конфигурацию сервера, отправив файл .htaccess, которые отвечает за правки в конфигурации сервера. Таким образом, мы можем можем добавить директиву AddType application/x-httpd-php в файл .htaccess, чтобы сервер обрабатывал наши файлы .php. Отправляем запрос. Файл успешно загружен.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_extension_blacklist_bypass/assets/5.png)

Теперь же мы отправим наш эксплоит с расшрением, которое мы указали в предыдущем запросе и допишем содержимое эксплоита. Наш файл успешно загружен.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_extension_blacklist_bypass/assets/6.png)

Обновляем страницу. Теперь мы можем найти наш запрос с содержимым файла.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_extension_blacklist_bypass/assets/7.png)

Подтверждаем ответ и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_extension_blacklist_bypass/assets/8.png)
