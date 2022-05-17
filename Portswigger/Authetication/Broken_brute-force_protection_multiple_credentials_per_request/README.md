# Broken brute-force protection, multiple credentials per request

## Theory

<h3>Ограничение скорости пользователя</h3>

Еще один способ, которым веб-сайты пытаются предотвратить атаки грубой силы, — это ограничение скорости пользователей. В этом случае слишком большое количество запросов на вход за короткий промежуток времени приводит к блокировке вашего IP-адреса. Как правило, IP-адрес можно разблокировать только одним из следующих способов:

* Автоматически по истечении определенного периода времени
* Вручную администратором
* Вручную пользователем после успешного прохождения CAPTCHA

Ограничение количества пользователей иногда предпочтительнее блокировки учетной записи, поскольку оно менее подвержено перечислению имен пользователей и атакам типа «отказ в обслуживании». Тем не менее, это все еще не является полностью безопасным. Злоумышленник может манипулировать своим кажущимся IP-адресом несколькими способами, чтобы обойти блокировку.

Поскольку ограничение основано на скорости HTTP-запросов, отправляемых с IP-адреса пользователя, иногда также можно обойти эту защиту, если вы сможете угадать несколько паролей с помощью одного запроса.

## Writeup

* Имеющиеся данные: 
    * Логин атакуемого пользователя: carlos

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_multiple_credentials_per_request/assets/1.png)

Страница аутентификации:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_multiple_credentials_per_request/assets/2.png)

Введем случайное значение пароля. В ответ мы получаем строку "Invalid username or password".

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_multiple_credentials_per_request/assets/3.png)

Создадим словарь, опирась на запрос, который мы отравляем. Наш словарь будет выглядеть следующим образом.
```
for pass in $(cat passwords); do  echo \"$pass\"\,; done > passwords_new
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_multiple_credentials_per_request/assets/4.png)

Подставим наши значения из словаря в список в поле password. На следующих скриншотах показаны начало и конец списка.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_multiple_credentials_per_request/assets/5.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_multiple_credentials_per_request/assets/6.png)

Отправляем запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_multiple_credentials_per_request/assets/7.png)

Запрос прошел успешно, поэтому снова перехватим запрос и переделаем его таким же образом. Это необходимо, т.к. мы пользуемся Burp Repeater-ом и нас не залогинит на сайте.

Отправляем запрос и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_multiple_credentials_per_request/assets/8.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_multiple_credentials_per_request/assets/9.png)
