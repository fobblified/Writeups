# Password reset broken logic

## Theory

<h3>Сброс паролей пользователей</h3>

На практике считается, что некоторые пользователи забывают свой пароль, поэтому у них обычно есть способ сбросить его. Поскольку обычная аутентификация на основе пароля, очевидно, невозможна в этом сценарии, веб-сайтам приходится полагаться на альтернативные методы, чтобы убедиться, что реальный пользователь сбрасывает свой собственный пароль. По этой причине функция сброса пароля по своей сути опасна и должна быть реализована безопасным образом.

<h3>Отправка паролей по электронной почте</h3>

Само собой разумеется, что отправка пользователям их текущего пароля никогда не должна быть возможной, если веб-сайт изначально надежно обрабатывает пароли. Вместо этого некоторые веб-сайты генерируют новый пароль и отправляют его пользователю по электронной почте.

Cледует избегать отправки постоянных паролей по небезопасным каналам. В этом случае безопасность зависит либо от сгенерированного пароля, срок действия которого истекает через очень короткий период, либо от того, что пользователь сразу же снова меняет свой пароль. В противном случае этот подход очень подвержен атакам типа «человек посередине».

Электронная почта также обычно не считается безопасной, учитывая, что почтовые ящики являются постоянными и не предназначены для безопасного хранения конфиденциальной информации. Многие пользователи также автоматически синхронизируют свои входящие сообщения между несколькими устройствами по незащищенным каналам.

<h3>Сброс паролей с помощью URL</h3>

Более надежным методом сброса паролей является отправка пользователям уникального URL-адреса, по которому они переходят на страницу сброса пароля. Менее безопасные реализации этого метода используют URL-адрес с легко угадываемым параметром, чтобы определить, какая учетная запись сбрасывается, например:
```
http://vulnerable-website.com/reset-password?user=victim-user
```

В этом примере злоумышленник может изменить параметр "user", чтобы он ссылался на любое имя пользователя, которое он идентифицировал. Затем они будут перенаправлены прямо на страницу, где они потенциально могут установить новый пароль для этого произвольного пользователя.

Лучшей реализацией этого процесса является создание токена с высокой энтропией, который трудно угадать, и создание URL-адреса сброса на его основе. В лучшем случае этот URL-адрес не должен содержать подсказок о том, пароль какого пользователя сбрасывается.
```
http://vulnerable-website.com/reset-password?token=a0ba0d1cb3b63d13822572fcff1a241895d893f659164d4cc550b421ebdd48a8
```

Когда пользователь посещает этот URL-адрес, система должна проверить, существует ли этот токен на сервере, и если да, то пароль какого пользователя он должен сбросить. Этот токен должен истечь через короткий промежуток времени и быть уничтожен сразу после сброса пароля.

Однако некоторые веб-сайты не могут снова проверить токен при отправке формы сброса. В этом случае злоумышленник может просто открыть форму сброса из своей учетной записи, удалить токен и использовать эту страницу для сброса пароля произвольного пользователя.


## Writeup

* Имеющиеся данные: 
    * Данные нашего пользователя: wiener:peter
    * Данные атакуемого пользователя: carlos

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_reset_broken_logic/assets/1.png)

Страница аутентификации:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_reset_broken_logic/assets/2.png)

Нажимаем на кнопку "Forgot password" для сброса пароля. Вводим имя нашего пользователя.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_reset_broken_logic/assets/3.png)

Откроем Email client для перехода по URL для сброса пароля.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_reset_broken_logic/assets/4.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_reset_broken_logic/assets/5.png)

Введем тот же пароль, что и у нашего пользователя.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_reset_broken_logic/assets/6.png)

Найдем POST запрос страницы с токеном **/forgot-password?temp-forgot-password-token=ftN...**

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_reset_broken_logic/assets/7.png)

Отправим запрос в Burp Repeater и удалим все значения токенов. Отправим запрос. Мы видим ответ, значит можно ввести значения пароля без токена.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_reset_broken_logic/assets/8.png)

Проделаем те же действия с восстановлением пароля пользователя wiener. Найдем новый POST запрос страницы с токеном **/forgot-password?temp-forgot-password-token=...mJ6**

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_reset_broken_logic/assets/9.png)

Отправляем запрос в Burp Repeater. Удалим все значения токенов и изменим имя пользова
теля на имя пользователя жертвы. Отправим запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_reset_broken_logic/assets/10.png)

Логинимся под нового пользователя и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_reset_broken_logic/assets/11.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Password_reset_broken_logic/assets/12.png)
