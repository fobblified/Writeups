# Subverting application logic

## Theory

<h3>Подрыв логики приложения</h3>

Рассмотрим приложение, которое позволяет пользователям входить в систему с именем пользователя и паролем.

Если пользователь отправляет имя пользователя и пароль приложение проверяет учетные данные отправляя SQL-запрос:
```
SELECT * FROM users WHERE username = 'USER' AND password = 'PASSWORD' 
```

Если запрос возвращает сведения о пользователе, то вход выполнен успешно. В противном случае запрос отклоняется.

Здесь злоумышленник может войти в систему как любой пользователь без пароля, просто используя последовательность комментариев SQL. 

Например, отправка имени пользователя "**administrator' --**" и пустого пароля приводит к следующему запросу:
```
SELECT * FROM users WHERE username = 'administrator' --' AND password = ''
```


## Writeup

Страница аутентификации:

![](https://github.com/fobblified/Writeups/tree/main/Portswigger/SQL_injection/Subverting_application_logic/assets/1.png)

Будем использовать следующее имя пользователя: "**administrator'--**"

![](https://github.com/fobblified/Writeups/tree/main/Portswigger/SQL_injection/Subverting_application_logic/assets/2.png)

Наш запрос будет выглядеть следующим образом:
```
username=administrator%27--&password=123
```

![](https://github.com/fobblified/Writeups/tree/main/Portswigger/SQL_injection/Subverting_application_logic/assets/3.png)

Отправляем запрос и получаем ответ приложения.

![](https://github.com/fobblified/Writeups/tree/main/Portswigger/SQL_injection/Subverting_application_logic/assets/4.png)
