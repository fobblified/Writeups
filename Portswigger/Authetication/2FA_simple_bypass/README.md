# 2FA simple bypass

## Theory

<h3>Обход двухфакторной аутентификации</h3>

Иногда реализация двухфакторной аутентификации настолько несовершенна, что ее можно полностью обойти.

Если пользователю сначала предлагается ввести пароль, а затем на отдельной странице предлагается ввести код подтверждения, пользователь фактически находится в состоянии «вошел в систему» ​​до того, как он ввел код подтверждения. В этом случае стоит проверить, можете ли вы сразу перейти к страницам «только для входа в систему» ​​после завершения первого шага аутентификации. Иногда вы обнаружите, что веб-сайт на самом деле не проверяет, выполнили ли вы второй шаг перед загрузкой страницы.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter
    * Данные атакуемого пользователя: carlos:montoya

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_simple_bypass/assets/1.png)

Страница аутентификации:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_simple_bypass/assets/2.png)

Вводим данные нашего пользователя. Перед нами появляется панель двухфакторной аутентификации.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_simple_bypass/assets/3.png)

Сверху нажимаем на кнопку "**Email client**", чтобы перейти на станицу "почты" для получения кода.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_simple_bypass/assets/4.png)

Вставляем код и входим в аккаунт. 

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_simple_bypass/assets/5.png)

В строке браузера моно заметить следующий url: **https://ac8a1f801ff4f677c09ef7ad00f40005.web-security-academy.net/my-account**. Нас перекидывает на страницу **/my-account**. Затем воздем с имеющимися данными пользователя carlos. На странице двухфакторной аутентификации в строке браузера изменим url добавив **/my-account**. 

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_simple_bypass/assets/6.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_simple_bypass/assets/7.png)
