# URL-based access control can be circumvented

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

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/URL-based_access_control_can_be_circumvented/assets/1.png)

Если мы перейдем на страницу /admin, мы получим сообщение "Access denied".

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/URL-based_access_control_can_be_circumvented/assets/2.png)

Перехватим запрос /admin. Отправим запрос в Repeater.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/URL-based_access_control_can_be_circumvented/assets/3.png)

Чтобы обойти блокировку, будем использовать заголовок **X-Original-URL**. Удалим в запросе страницу /admin, оставив только /. Добавим заголовок с необходимой страницей. Отправляем запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/URL-based_access_control_can_be_circumvented/assets/4.png)

Модифицируем запрос, чтобы удалить пользователя carlos. Необходимые данные для удаления пользователя можно найти в ответе приложения.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/URL-based_access_control_can_be_circumvented/assets/5.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/URL-based_access_control_can_be_circumvented/assets/6.png)
