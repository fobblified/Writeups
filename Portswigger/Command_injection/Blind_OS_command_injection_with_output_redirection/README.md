# Blind OS command injection with output redirection

## Theory

<h3>Использование слепой инъекции команд ОС путем перенаправления вывода</h3>

Вы можете перенаправить вывод введенной команды в файл в корневом каталоге веб-сайта, который затем можно получить с помощью браузера. Например, если приложение обслуживает статические ресурсы из расположения файловой системы /var/www/static, вы можете отправить следующий ввод:
```
& whoami > /var/www/static/whoami.txt &
```

Символ > отправляет вывод команды whoami в указанный файл. Затем вы можете использовать браузер для получения https://vulnerable-website.com/whoami.txt, чтобы получить файл и просмотреть вывод введенной команды.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_output_redirection/assets/1.png)

Перейдем на страницу "Submit feedback".

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_output_redirection/assets/2.png)

Заполним поля для ввода случайными данными и перехватим запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_output_redirection/assets/3.png)

Модифицируем его перенаправив выход вывода команды whoami в файл.
```
email=111%40gmail.com||whoami+>+/var/www/images/whoami.txt||
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_output_redirection/assets/4.png)

Отправляем запрос. Перейдем на основную страницу, обновим ее и перехватим запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_output_redirection/assets/5.png)

Откроем наш файл используя directory traversal.
```
/image?filename=whoami.txt
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_output_redirection/assets/6.png)

Отправляем запрос и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_output_redirection/assets/7.png)
