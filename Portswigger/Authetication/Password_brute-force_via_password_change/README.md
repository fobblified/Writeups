# Password brute-force via password сhange

## Theory

<h3>Изменение паролей пользователей</h3>

Как правило, для смены пароля необходимо дважды ввести текущий пароль, а затем новый пароль. Эти страницы в основном основаны на том же процессе проверки совпадения имен пользователей и текущих паролей, что и обычная страница входа. Следовательно, эти страницы могут быть уязвимы для одних и тех же методов.

Функция смены пароля может быть особенно опасна, если позволяет злоумышленнику получить к ней прямой доступ, не входя в систему в качестве пользователя-жертвы. Например, если имя пользователя указано в скрытом поле, злоумышленник может изменить это значение в запросе, нацелив его на произвольных пользователей. Потенциально это может быть использовано для перечисления имен пользователей и перебора паролей.

## Writeup

* Имеющиеся данные: 
    * Данные нашего пользователя: wiener:peter
    * Данные атакуемого пользователя: carlos

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_brute-force_via_password_change/assets/1.png)

Страница аутентификации:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_brute-force_via_password_change/assets/2.png)

Логинимся и входим в аккаунт. Вводим наш пароль и тот на который мы хотим сменить.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_brute-force_via_password_change/assets/3.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_brute-force_via_password_change/assets/4.png)

Снова логинимся, но уже с новым паролем. Введем неправильно значение сущствующего пароля и разные значения пароля для смены. Появляется сообщение о том, что текущий пароль неверный. 

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_brute-force_via_password_change/assets/5.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_brute-force_via_password_change/assets/6.png)

Проделаем то же самое и перехватим запрос. Отправляем его в Burp Intruder. Изменим значение пользователя на carlos и внесем значение пароля в специальные символы.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_brute-force_via_password_change/assets/7.png)

Вставим словарь для перебора. Отсортируем ответы по длине и замечаем, что один ответ выделяется.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_brute-force_via_password_change/assets/8.png)

Логинимся под пользователем carlos. Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_brute-force_via_password_change/assets/9.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_brute-force_via_password_change/assets/10.png)
