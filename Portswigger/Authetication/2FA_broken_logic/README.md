# 2FA broken logic

## Theory

<h3>Несовершенная логика двухфакторной проверки</h3>

Иногда ошибочная логика двухфакторной аутентификации означает, что после того, как пользователь завершил начальный этап входа в систему, веб-сайт не проверяет должным образом, что тот же пользователь выполняет второй этап.

Например, пользователь входит в систему со своими обычными учетными данными на первом этапе следующим образом:
```
POST /login-steps/first HTTP/1.1
Host: vulnerable-website.com
...
username=carlos&password=qwerty
```

Затем им назначается файл cookie, относящийся к их учетной записи, прежде чем перейти ко второму этапу процесса входа в систему:
```
HTTP/1.1 200 OK
Set-Cookie: account=carlos

GET /login-steps/second HTTP/1.1
Cookie: account=carlos
```

При отправке кода подтверждения запрос использует этот файл cookie, чтобы определить, к какой учетной записи пользователь пытается получить доступ:
```
POST /login-steps/second HTTP/1.1
Host: vulnerable-website.com
Cookie: account=carlos
...
verification-code=123456
```

В этом случае злоумышленник может войти в систему, используя свои собственные учетные данные, но затем изменить значение файла cookie учетной записи на любое произвольное имя пользователя при отправке кода подтверждения:
```
POST /login-steps/second HTTP/1.1
Host: vulnerable-website.com
Cookie: account=victim-user
...
verification-code=123456
```

Это чрезвычайно опасно, если злоумышленник затем сможет взломать код подтверждения, поскольку это позволит им входить в учетные записи произвольных пользователей, полностью основываясь на их имени пользователя.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter
    * Данные атакуемого пользователя: carlos

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_broken_logic/assets/1.png)

Страница аутентификации:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_broken_logic/assets/2.png)

Вводим данные нашего пользователя. Перед нами появляется панель двухфакторной аутентификации.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_broken_logic/assets/3.png)

Сверху нажимаем на кнопку "**Email client**", чтобы перейти на станицу "почты" для получения кода.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_broken_logic/assets/4.png)

Вставляем код и входим в аккаунт. 

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_broken_logic/assets/5.png)

Во вкладке **HTTP History** найдем наш **GET** запрос **/login2**.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_broken_logic/assets/6.png)

Отправим его в Burp Repeater и изменим имени пользователя на имя пользователя жертвы. Оправляем запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_broken_logic/assets/7.png)

Так же во вкладке **HTTP History** найдем наш **POST** запрос **/login2**.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_broken_logic/assets/8.png)

Отправляем наш запрос в Burp Intruder. Вставим код верификации в специальные символы.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_broken_logic/assets/9.png)

Во вкладке **Payload** выбираем метод **brute forcer**. Оставляем только цифры и выставляем значения на 4.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_broken_logic/assets/10.png)

Сортируем запросы по длине. Нажав на нужный нам запрос, откроем меню и выберем пункт **Show response in browser**.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_broken_logic/assets/11.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_broken_logic/assets/12.png)

Копирует url и вставляем в браузер.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_broken_logic/assets/13.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/2FA_broken_logic/assets/14.png)
