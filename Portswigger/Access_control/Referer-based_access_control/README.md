# Referer-based access control

## Theory

<h3>Контроль доступа на основе ссылок</h3>

Некоторые веб-сайты основывают контроль доступа на заголовке Referer, отправленном в HTTP-запросе. Заголовок Referer обычно добавляется к запросам браузерами, чтобы указать страницу, с которой был инициирован запрос.

Например, предположим, что приложение надежно контролирует доступ к главной административной странице в /admin, но для подстраниц, таких как /admin/deleteUser, проверяет только заголовок Referer. Если заголовок Referer содержит основной URL-адрес /admin, то запрос разрешен.

В этой ситуации, поскольку заголовок Referer может полностью контролироваться злоумышленником, он может подделать прямые запросы к конфиденциальным подстраницам, предоставив требуемый заголовок Referer, и, таким образом, получить несанкционированный доступ.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter
    * Данные администратора: administrator:admin

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Referer-based_access_control/assets/1.png)

Логинимся под администратора.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Referer-based_access_control/assets/2.png)

У нас есть доступ к панели управления привилегиями.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Referer-based_access_control/assets/3.png)

Повышаем привилегии пользователя. Перехватываем запрос, отправляем его в Repeater.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Referer-based_access_control/assets/4.png)

Зайдем на этот же сайт во вкладке инкогнито и залогинимся под пользователя wiener.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Referer-based_access_control/assets/5.png)

Обновляем страницу и перехватываем запрос. Скопируем значение параметра session.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Referer-based_access_control/assets/6.png)

Изменим значение сессии и имя пользователя в запросе, который мы отправили в Repeater. Отправляем запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Referer-based_access_control/assets/7.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Referer-based_access_control/assets/8.png)
