# Brute-forcing a stay-logged-in cookie

## Theory

<h3>Сохранение входа пользователей</h3>

Общей особенностью является возможность оставаться в системе даже после закрытия сеанса браузера. Обычно это простой флажок с надписью «Запомнить меня» или «Оставаться в системе».

Эта функциональность часто реализуется путем создания токена «запомнить меня», который затем сохраняется в постоянном файле cookie. Поскольку обладание файлом cookie эффективно позволяет обойти весь процесс входа в систему, рекомендуется, чтобы этот файл cookie было непрактично угадывать. Однако некоторые веб-сайты генерируют этот файл cookie на основе предсказуемой конкатенации статических значений, таких как имя пользователя и метка времени. Некоторые даже используют пароль как часть файла cookie. Этот подход особенно опасен, если злоумышленник может создать свою собственную учетную запись, поскольку он может изучить свой собственный файл cookie и потенциально определить, как он создается. Узнав формулу, они могут попытаться взломать файлы cookie других пользователей.

Некоторые веб-сайты предполагают, что если файл cookie каким-то образом зашифрован, его нельзя будет угадать, даже если он использует статические значения. Это может быть правдой, если все сделано правильно, "шифрование" файла cookie с использованием простого двустороннего кодирования, такого как Base64, не обеспечивает никакой защиты. Даже правильное шифрование с односторонней хеш-функцией не является полностью надежным. Если злоумышленник может легко идентифицировать алгоритм хеширования и не использовать соль, он потенциально может взломать файл cookie, просто хешировав свои списки слов. Этот метод можно использовать для обхода ограничений на попытки входа в систему, если аналогичный лимит не применяется к догадкам файлов cookie.

## Writeup

* Имеющиеся данные: 
    * Данные нашего пользователя: wiener:peter
    * Данные атакуемого пользователя: carlos

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Brute-forcing_a_stay-logged-in_cookie/assets/1.png)

Страница аутентификации:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Brute-forcing_a_stay-logged-in_cookie/assets/2.png)

Вводим данные нашего пользователя, нажимаем на кнопку "Stay logged in".

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Brute-forcing_a_stay-logged-in_cookie/assets/3.png)

Во вкладке **HTTP History** найдем GET запрос /my-account. Отправим запрос в Burp Decoder.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Brute-forcing_a_stay-logged-in_cookie/assets/4.png)

Запрос в Burp Decoder-е будет выглядеть следующим образом.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Brute-forcing_a_stay-logged-in_cookie/assets/5.png)

Оставляем только значение "**stay logged in**", удалив все остальное. Декодируем из base64. Мы можем заметить наше имя пользователя. Попробуем вставить хэшированное значение в crackstation.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Brute-forcing_a_stay-logged-in_cookie/assets/6.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Brute-forcing_a_stay-logged-in_cookie/assets/7.png)

Вернемся на вкладку **HTTP History** и найдем тот же запрос. 

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Brute-forcing_a_stay-logged-in_cookie/assets/8.png)

Отправим его в Burp Intruder. Будем перебираться значение cookie.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Brute-forcing_a_stay-logged-in_cookie/assets/9.png)

Найдем во вкладке **Payload** панель **Payload Processing**. Нажмем на кнопку Add и выставим значение хэша - md5, добавлять часть в начало - carlos:, кодировка - base64.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Brute-forcing_a_stay-logged-in_cookie/assets/10.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Brute-forcing_a_stay-logged-in_cookie/assets/11.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Brute-forcing_a_stay-logged-in_cookie/assets/12.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Brute-forcing_a_stay-logged-in_cookie/assets/13.png)

Наша панель будет выглядеть следующим образом.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Brute-forcing_a_stay-logged-in_cookie/assets/14.png)

Запускаем перебор и сортируем по длине ответа.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Brute-forcing_a_stay-logged-in_cookie/assets/15.png)

Сразу после нахождения нужного хэша получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Brute-forcing_a_stay-logged-in_cookie/assets/16.png)
