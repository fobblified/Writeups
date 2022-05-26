# Blind OS command injection with time delays

## Theory

<h3>Слепые уязвимости внедрения команд ОС</h3>

Многие случаи внедрения команд ОС являются слепыми уязвимостями. Это означает, что приложение не возвращает выходные данные команды в ответе HTTP. Слепые уязвимости по-прежнему можно эксплуатировать, но для этого требуются другие методы.

Рассмотрим веб-сайт, который позволяет пользователям отправлять отзывы о сайте. Пользователь вводит свой адрес электронной почты и сообщение обратной связи. Затем приложение на стороне сервера создает электронное письмо администратору сайта, содержащее отзыв. Для этого он обращается к почтовой программе с отправленными реквизитами. Например:
```
mail -s "This site is great" -aFrom:peter@normal-user.net feedback@vulnerable-website.com
```

Вывод команды mail (если есть) не возвращается в ответах приложения, поэтому использование полезной нагрузки echo неэффективно. В этой ситуации вы можете использовать множество других методов для обнаружения и использования уязвимости.

<h3>Обнаружение внедрения слепых команд ОС с использованием временных задержек</h3>

Вы можете использовать введенную команду, которая вызовет временную задержку, позволяя вам подтвердить, что команда была выполнена на основе времени, которое требуется приложению для ответа. Команда ping — эффективный способ сделать это, поскольку она позволяет указать количество отправляемых ICMP-пакетов и, следовательно, время, необходимое для выполнения команды:
```
& ping -c 10 127.0.0.1 &
```

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_time_delays/assets/1.png)

Перейдем на страницу "Submit feedback".

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_time_delays/assets/2.png)

Заполним поля для ввода случайными данными и перехватим запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_time_delays/assets/3.png)

Модифицируем его с использованием команды ping -c, чтобы узнать, есть ли command injection на странице.
```
email=111%40gmail.com||ping+-c+10+127.0.0.1||
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_time_delays/assets/4.png)

Отправляем запрос, ждем 10 секунд, получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/Blind_OS_command_injection_with_time_delays/assets/5.png)
