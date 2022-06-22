# Method-based access control can be circumvented

## Theory

<h3>Нарушенный контроль доступа из-за неправильной конфигурации платформы</h3>

Некоторые приложения применяют контроль доступа на уровне платформы, ограничивая доступ к определенным URL-адресам и методам HTTP в зависимости от роли пользователя. Например, приложение может настроить следующие правила:
```
DENY: POST, /admin/deleteUser, managers
```

Это правило запрещает доступ к методу POST по URL-адресу /admin/deleteUser для пользователей из группы менеджеров. В этой ситуации могут использоваться разные методы, которые приведут к обходу контроля доступа.

Некоторые платформы приложений поддерживают различные нестандартные заголовки HTTP, которые можно использовать для переопределения URL-адреса в исходном запросе, например X-Original-URL и X-Rewrite-URL. Если веб-сайт использует строгие внешние элементы управления для ограничения доступа на основе URL-адреса, но приложение позволяет переопределить URL-адрес с помощью заголовка запроса, то можно обойти элементы управления доступом, используя запрос, подобный следующему:
```
POST / HTTP/1.1
X-Original-URL: /admin/deleteUser
...
```

Альтернативная атака может возникнуть в отношении метода HTTP, используемого в запросе. Приведенные выше интерфейсные элементы управления ограничивают доступ на основе URL-адреса и метода HTTP. Некоторые веб-сайты терпимы к альтернативным методам HTTP-запроса при выполнении действия. Если злоумышленник может использовать метод GET (или другой) для выполнения действий с ограниченным URL-адресом, он может обойти контроль доступа, реализованный на уровне платформы.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter
    * Данные администратора: administrator:admin

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Method-based_access_control_can_be_circumvented/assets/1.png)

Логинимся под администратора.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Method-based_access_control_can_be_circumvented/assets/2.png)

Переходим в админ панель. На данной странице мы можем изменять привилегии пользователя carlos.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Method-based_access_control_can_be_circumvented/assets/3.png)

Нажмем на кнопку "Upgrade user" и перехватим запрос. Отправляем его в Repeater.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Method-based_access_control_can_be_circumvented/assets/4.png)

Откроем анонимный режим браузера в новой странице. Откроем страницу аутентификации нашего сайта и залогинимся под пользователя wiener.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Method-based_access_control_can_be_circumvented/assets/5.png)

Обновим страницу и перехватим запрос. Отправляем его в Repeater.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Method-based_access_control_can_be_circumvented/assets/6.png)

Скопируем параметр сессии из запроса страницы пользователя wiener и вставим вместо параметра сессии запроса повышения привилегий. Отправив запрос, мы можем заметить сообщение о том, что пользователь не авторизован.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Method-based_access_control_can_be_circumvented/assets/7.png)

Чтобы обойти блокировку будем использовать тот же самый запрос, изменив его метод на GET. Для этого откроем выпадающее меню и выберем функцию "Change request method". 

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Method-based_access_control_can_be_circumvented/assets/8.png)

Изменим имя пользователя на wiener. Отправляем запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Method-based_access_control_can_be_circumvented/assets/9.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Method-based_access_control_can_be_circumvented/assets/10.png)
