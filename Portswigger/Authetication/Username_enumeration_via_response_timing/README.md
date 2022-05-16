# Username enumeration via response timing

## Theory

<h3>Перечисление имен пользователей</h3>

При попытке брутфорса нужно обратить особое внимание на любые различия в:

* **Коды состояния:** во время брутфорса возвращаемый код состояния HTTP, скорее всего, будет одинаковым для подавляющего большинства догадок, потому что большинство из них будут неверными. Если ответ возвращает другой код состояния, это явный признак того, что имя пользователя было правильным. Для веб-сайтов рекомендуется всегда возвращать один и тот же код состояния независимо от результата, но эта практика не всегда соблюдается.
* **Сообщения об ошибках:** иногда возвращаемое сообщение об ошибке отличается в зависимости от того, являются ли неверными имя пользователя и пароль или только пароль. Для веб-сайтов рекомендуется использовать одинаковые общие сообщения в обоих случаях, но иногда могут возникать небольшие опечатки. Всего один неуместный символ делает два сообщения различимыми, даже в тех случаях, когда символ не виден на отображаемой странице.
* **Время отклика:** если большинство запросов были обработаны с одинаковым временем отклика, любые отклонения от этого предполагают, что за кулисами происходило что-то другое. Это еще один признак того, что предполагаемое имя пользователя может быть правильным. Например, веб-сайт может проверить правильность пароля только в том случае, если имя пользователя действительно. Этот дополнительный шаг может привести к небольшому увеличению времени отклика. Это может быть незаметным, но злоумышленник может сделать эту задержку более очевидной, введя слишком длинный пароль, на обработку которого веб-сайту требуется значительно больше времени.


## Writeup

* Имеющиеся данные: wiener:peter

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_response_timing/assets/1.png)

Страница аутентификации:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_response_timing/assets/2.png)

Введем случайные значения имени пользователя и пароля. В ответ мы получаем строку "Invalid username or password.".

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_response_timing/assets/3.png)

Отправив еще несколько таких запросов, мы получаем соощение о бане: "**You have made too many incorrect login attempts. Please try again in 30 minute(s).**".

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_response_timing/assets/4.png)

Для того, чтобы обойти блокировку, будем использовать заголовок **X-Forwarded-For: \<number>**. Будем изменять его значение при каждой отправке запроса.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_response_timing/assets/5.png)

Подставим имеющееся имя пользователя. Мы можем увидеть, что время обработки запроса составляет 102 millis.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_response_timing/assets/6.png)

Увеличив длину пароля, можно заметить, что время обработки запроса увеличилось до 1686 millis.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_response_timing/assets/7.png)

Отправим наш запрос в Burp Intruder и вставим имя пользователя и значение для X-Forwarded-For в специальные символы. Вставим нужный словарь для перебора и запустим атаку. 

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_response_timing/assets/8.png)

Во вкладке **Columns** выберем для отображения два новых пункта - **Response received** и **Response completed**. Отсортируем значения и увидим, что одно имя пользователя сильно отличается по времени обработки.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_response_timing/assets/9.png)

Проделаем то же самое с паролем, только подставив уже известное нам имя пользователя.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_response_timing/assets/10.png)

Сортируем по длине ответа.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_response_timing/assets/11.png)

Снова на странице аутентификации введем полученные данные от пользователя и перехватим запрос. Вставим заголовок X-Forwarded-For, т.к. мы все еще в бане. Отправим запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_response_timing/assets/12.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_response_timing/assets/13.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_response_timing/assets/14.png)
