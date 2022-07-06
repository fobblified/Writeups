# Basic SSRF against another back-end system

## Theory

<h3>SSRF-атаки на другие серверные системы</h3>

Другой тип доверительных отношений, который часто возникает при подделке запросов на стороне сервера, заключается в том, что сервер приложений может взаимодействовать с другими внутренними системами, недоступными для пользователей напрямую. Эти системы часто имеют немаршрутизируемые частные IP-адреса. Поскольку серверные системы обычно защищены топологией сети, они часто имеют более слабую защиту. Во многих случаях внутренние серверные системы содержат конфиденциальные функции, доступ к которым без аутентификации может получить любой, кто может взаимодействовать с системами.

Предположим, что на внутреннем URL-адресе https://192.168.0.68/admin есть административный интерфейс. Здесь злоумышленник может использовать уязвимость SSRF для доступа к административному интерфейсу, отправив следующий запрос:
```
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://192.168.0.68/admin
```

## Writeup

* Имеющиеся данные: 
    * Диапазон ip-адресов: 192.168.0.X:8080

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Basic_SSRF_against_another_back-end_system/assets/1.png)

Выберем любой продукт.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Basic_SSRF_against_another_back-end_system/assets/2.png)

Перейдем на его страницу и спустимся в самый низ.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Basic_SSRF_against_another_back-end_system/assets/3.png)

Нажмем на кнопку "Check stock" и перехватим запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Basic_SSRF_against_another_back-end_system/assets/4.png)

Изменим stockApi на http://192.168.0.X:8080/admin. Отправляем запрос в Intruder и помещаем X в специальные символы. Мы будем вести перебор от 1 до 255.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Basic_SSRF_against_another_back-end_system/assets/5.png)

Запускаем атаку. Сортируем по коду ответа и получаем единственный ответ с кодом 200. Нужная нам страница находится по адресу 192.168.0.126:8080/...

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Basic_SSRF_against_another_back-end_system/assets/6.png)

Скопируем функцию удаления пользователя и вставим в stockApi. Отправляем запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Basic_SSRF_against_another_back-end_system/assets/7.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Basic_SSRF_against_another_back-end_system/assets/8.png)
