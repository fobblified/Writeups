# 2FA bypass using a brute-force attack

## Theory

<h3>Подбор кодов подтверждения 2FA</h3>

Как и в случае с паролями, веб-сайты должны принимать меры для предотвращения перебора проверочного кода 2FA. Это особенно важно, потому что код часто представляет собой простое число из 4 или 6 цифр. Без адекватной защиты от грубой силы взломать такой код будет тривиально.

Некоторые веб-сайты пытаются предотвратить это путем автоматического выхода пользователя из системы, если он вводит определенное количество неверных кодов подтверждения. На практике это неэффективно, поскольку продвинутый злоумышленник может даже автоматизировать этот многоэтапный процесс, создав макросы для Burp Intruder.

## Writeup

* Имеющиеся данные: 
    * Данные атакуемого пользователя: carlos:montoya

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_bypass_using_a_brute-force_attack/assets/1.png)

Страница аутентификации:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_bypass_using_a_brute-force_attack/assets/2.png)

Вводим данные нашего пользователя. Перед нами появляется панель двухфакторной аутентификации.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_bypass_using_a_brute-force_attack/assets/3.png)

Введя несколько раз неправильно код верификации нас перебрасывает на страницу аутентификации.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_bypass_using_a_brute-force_attack/assets/4.png)

Перейдем в меню **Project option -> Session** для настройки макроса. Нам понадобится панель **Session Handling Rules**.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_bypass_using_a_brute-force_attack/assets/5.png)

Нажимаем на кнопку Add. В панели **Scope -> URL Scope** выбираем пункт **Include all URLs**.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_bypass_using_a_brute-force_attack/assets/6.png)

В панели **Details** нажмем на кнопку Add и выберем пункт **Run a macro**.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_bypass_using_a_brute-force_attack/assets/7.png)

Нажимаем на кнопку Add.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_bypass_using_a_brute-force_attack/assets/8.png)

Выбираем GET и POST запросы /login и GET запрос /login2. Нажимаем на кнопку OK.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_bypass_using_a_brute-force_attack/assets/9.png)

Затем выберем пункт **Test macro**.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_bypass_using_a_brute-force_attack/assets/10.png)

Перейдем к запросу /login2 и выделим строку "Please enter your 4-digit security code", чтобы удостоверится, что все работает.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_bypass_using_a_brute-force_attack/assets/11.png)

Нажимаем на кнопку OK. У нас появился наш макрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_bypass_using_a_brute-force_attack/assets/12.png)

Во вкладке **HTTP History** найдем наш POST запрос /login2.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_bypass_using_a_brute-force_attack/assets/13.png)

Отправляем наш запрос в Burp Intruder. Вставим код верификации в специальные символы.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_bypass_using_a_brute-force_attack/assets/14.png)

Во вкладке **Payload** выбираем метод **Numbers**. Выставляем следующие параметры, чтобы наше число было 4-х значным.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_bypass_using_a_brute-force_attack/assets/15.png)

Во меню **Resource Pool** выставим значение попыток - 1. Начнем атаку.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_bypass_using_a_brute-force_attack/assets/16.png)

Сортируем запросы по коду ответа. Нажав на нужный нам запрос, откроем меню и выберем пункт **Show response in browser**.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_bypass_using_a_brute-force_attack/assets/17.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_bypass_using_a_brute-force_attack/assets/18.png)

Копируем url и вставляем в браузер.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_bypass_using_a_brute-force_attack/assets/19.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_bypass_using_a_brute-force_attack/assets/20.png)
