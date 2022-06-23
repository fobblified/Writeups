# User ID controlled by request parameter with password disclosure

## Theory

<h3>Повышение привилегий от горизонтального до вертикального</h3>

Часто атаку с горизонтальным повышением привилегий можно превратить в повышение привилегий по вертикали путем компрометации более привилегированного пользователя. Например, горизонтальная эскалация может позволить злоумышленнику сбросить или перехватить пароль, принадлежащий другому пользователю. Если злоумышленник нацелится на пользователя с правами администратора и скомпрометирует его учетную запись, он сможет получить административный доступ и, таким образом, выполнить вертикальное повышение привилегий.

Например, злоумышленник может получить доступ к странице учетной записи другого пользователя, используя технику подделки параметров  для горизонтального повышения привилегий:
```
https://insecure-website.com/myaccount?id=456
```

Если целевой пользователь является администратором приложения, злоумышленник получит доступ к странице административной учетной записи. На этой странице может быть раскрыт пароль администратора или предоставлены средства для его изменения, или может быть предоставлен прямой доступ к привилегированным функциям.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_password_disclosure/assets/1.png)

Логинимся с имеющимися данными.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_password_disclosure/assets/2.png)

Обновим страницу и перехватим запрос. Изменим id пользователя на administrator и отправим запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_password_disclosure/assets/3.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_password_disclosure/assets/4.png)

Получаем доступ к смене пароля админа. 

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_password_disclosure/assets/5.png)

Нажмем на кнопку смены пароля и перехватим запрос. Внутри запроса мы можем увидеть пароль администратора. Копируем его и отправляем запрос без изменений.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_password_disclosure/assets/6.png)

Логинимся с полученными данными. Получаем доступ к панели админа.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_password_disclosure/assets/7.png)

Переходим в панель администратора и удаляем пользователя carlos.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_password_disclosure/assets/8.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_password_disclosure/assets/9.png)
