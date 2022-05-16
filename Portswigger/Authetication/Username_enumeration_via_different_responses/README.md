# Username enumeration via different responses

## Theory

<h3>Перечисление имен пользователей</h3>

При попытке брутфорса нужно обратить особое внимание на любые различия в:

* **Коды состояния:** во время брутфорса возвращаемый код состояния HTTP, скорее всего, будет одинаковым для подавляющего большинства догадок, потому что большинство из них будут неверными. Если ответ возвращает другой код состояния, это явный признак того, что имя пользователя было правильным. Для веб-сайтов рекомендуется всегда возвращать один и тот же код состояния независимо от результата, но эта практика не всегда соблюдается.
* **Сообщения об ошибках:** иногда возвращаемое сообщение об ошибке отличается в зависимости от того, являются ли неверными имя пользователя и пароль или только пароль. Для веб-сайтов рекомендуется использовать одинаковые общие сообщения в обоих случаях, но иногда могут возникать небольшие опечатки. Всего один неуместный символ делает два сообщения различимыми, даже в тех случаях, когда символ не виден на отображаемой странице.
* **Время отклика:** если большинство запросов были обработаны с одинаковым временем отклика, любые отклонения от этого предполагают, что за кулисами происходило что-то другое. Это еще один признак того, что предполагаемое имя пользователя может быть правильным. Например, веб-сайт может проверить правильность пароля только в том случае, если имя пользователя действительно. Этот дополнительный шаг может привести к небольшому увеличению времени отклика. Это может быть незаметным, но злоумышленник может сделать эту задержку более очевидной, введя слишком длинный пароль, на обработку которого веб-сайту требуется значительно больше времени.


## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_different_responses/assets/1.png)

Страница аутентификации:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_different_responses/assets/2.png)

Введем случайные значения имени пользователя и пароля. В ответ мы получаем строку "Invalid username".

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_different_responses/assets/3.png)

Отправим наш запрос в Burp Intruder и вставим имя пользователя для перебора в специальные символы. Выберем словарь и начнем атаку.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_different_responses/assets/4.png)

Отсортируем наши ответы по длине. Единственное имя пользователя выделяется.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_different_responses/assets/5.png)

Проделаем то же самое с паролем, только подставив уже известное нам имя пользователя.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_different_responses/assets/6.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_different_responses/assets/7.png)

Вводим логин и пароль и проходим аутентификацию. Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_different_responses/assets/8.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_different_responses/assets/9.png)
