# User role can be modified in user profile

## Theory

<h3>Методы управления доступом на основе параметров</h3>

Некоторые приложения определяют права доступа или роль пользователя при входе в систему, а затем сохраняют эту информацию в контролируемом пользователем расположении, таком как скрытое поле, файл cookie или заданный параметр строки запроса. Приложение принимает последующие решения по управлению доступом на основе представленного значения. Например:
```
https://insecure-website.com/login/home.jsp?admin=true
https://insecure-website.com/login/home.jsp?role=1
```

Этот подход принципиально небезопасен, поскольку пользователь может просто изменить значение и получить доступ к функциональным возможностям, к которым у него нет полномочий, например к административным функциям.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_role_can_be_modified_in_user_profile/assets/1.png)

Логинимся с имеющимися данными. Обновим наш почтовый адрес. Перехватим этот запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_role_can_be_modified_in_user_profile/assets/2.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_role_can_be_modified_in_user_profile/assets/3.png)

Отправляем его в Repeater. Отправляем запрос. Мы можем заметить параметр roleid, который отвечает за права, которые выдаются пользователю.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_role_can_be_modified_in_user_profile/assets/4.png)

Модифицируем наш запрос, добавив параметр roleid и изменив его значение на 2. Отправляем запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_role_can_be_modified_in_user_profile/assets/5.png)

Теперь мы имеем доступ к панели админа.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_role_can_be_modified_in_user_profile/assets/6.png)

Переходим в панель админа и удаляем пользователя carlos.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_role_can_be_modified_in_user_profile/assets/7.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_role_can_be_modified_in_user_profile/assets/8.png)
