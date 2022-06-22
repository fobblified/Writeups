# User ID controlled by request parameter

## Theory

<h3>Горизонтальное повышение привилегий</h3>

Горизонтальное повышение привилегий возникает, когда пользователь может получить доступ к ресурсам, принадлежащим другому пользователю, вместо своих собственных ресурсов этого типа. Например, если сотрудник должен иметь доступ только к своим записям о занятости и заработной плате, но на самом деле может также получить доступ к записям других сотрудников, то это горизонтальное повышение привилегий.

Атаки с горизонтальным повышением привилегий могут использовать те же типы методов эксплойта, что и вертикальное повышение привилегий. Например, обычно пользователь может получить доступ к странице своей учетной записи, используя URL-адрес, подобный следующему:
```
https://insecure-website.com/myaccount?id=123
```

Теперь, если злоумышленник изменит значение параметра id на значение другого пользователя, он может получить доступ к странице учетной записи другого пользователя с соответствующими данными и функциями.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter/assets/1.png)

Логинимся с имеющимися данными.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter/assets/2.png)

Обновим страницу и перехватим запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter/assets/3.png)

Изменим id пользователя на carlos.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter/assets/4.png)

Отправляем запрос. Теперь мы имеем доступ к странице пользователя carlos. Копируем ключ API.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter/assets/5.png)

Переходим к подтвеждению ответа.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter/assets/6.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter/assets/7.png)
