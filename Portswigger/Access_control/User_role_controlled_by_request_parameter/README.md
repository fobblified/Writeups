# User role controlled by request parameter

## Theory

<h3>Методы управления доступом на основе параметров</h3>

Некоторые приложения определяют права доступа или роль пользователя при входе в систему, а затем сохраняют эту информацию в контролируемом пользователем расположении, таком как скрытое поле, файл cookie или заданный параметр строки запроса. Приложение принимает последующие решения по управлению доступом на основе представленного значения. Например:
```
https://insecure-website.com/login/home.jsp?admin=true
https://insecure-website.com/login/home.jsp?role=1
```

Этот подход принципиально небезопасен, поскольку пользователь может просто изменить значение и получить доступ к функциональным возможностям, к которым у него нет полномочий, например к административным функциям.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_role_controlled_by_request_parameter/assets/1.png)

Мы будем перехватывать все запросы и изменять параметр Admin=false, на Admin=true. 

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_role_controlled_by_request_parameter/assets/2.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_role_controlled_by_request_parameter/assets/3.png)

Таким образом, мы можем получить доступ к панели админа.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_role_controlled_by_request_parameter/assets/4.png)

Удаляем пользователя carlos и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_role_controlled_by_request_parameter/assets/5.png)
