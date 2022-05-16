# Username enumeration via subtly different responses

## Theory

<h3>Перечисление имен пользователей</h3>

При попытке брутфорса нужно обратить особое внимание на любые различия в:

* **Коды состояния:** во время брутфорса возвращаемый код состояния HTTP, скорее всего, будет одинаковым для подавляющего большинства догадок, потому что большинство из них будут неверными. Если ответ возвращает другой код состояния, это явный признак того, что имя пользователя было правильным. Для веб-сайтов рекомендуется всегда возвращать один и тот же код состояния независимо от результата, но эта практика не всегда соблюдается.
* **Сообщения об ошибках:** иногда возвращаемое сообщение об ошибке отличается в зависимости от того, являются ли неверными имя пользователя и пароль или только пароль. Для веб-сайтов рекомендуется использовать одинаковые общие сообщения в обоих случаях, но иногда могут возникать небольшие опечатки. Всего один неуместный символ делает два сообщения различимыми, даже в тех случаях, когда символ не виден на отображаемой странице.
* **Время отклика:** если большинство запросов были обработаны с одинаковым временем отклика, любые отклонения от этого предполагают, что за кулисами происходило что-то другое. Это еще один признак того, что предполагаемое имя пользователя может быть правильным. Например, веб-сайт может проверить правильность пароля только в том случае, если имя пользователя действительно. Этот дополнительный шаг может привести к небольшому увеличению времени отклика. Это может быть незаметным, но злоумышленник может сделать эту задержку более очевидной, введя слишком длинный пароль, на обработку которого веб-сайту требуется значительно больше времени.


## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_subtly_different_responses/assets/1.png)

Страница аутентификации:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_subtly_different_responses/assets/2.png)

Введем случайные значения имени пользователя и пароля. В ответ мы получаем строку "Invalid username or password.".

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_subtly_different_responses/assets/3.png)

Отправим наш запрос в Burp Intruder и вставим имя пользователя для перебора в специальные символы.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_subtly_different_responses/assets/4.png)

Зайдем во вкладку **options** Burp Intruder-а и найдем панель **Grep - Extract**. Нажмем на кнопку **Add**, чтобы добавить поиск по фразе.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_subtly_different_responses/assets/5.png)

Выделим ответ от сервера, а именно "**Invalid username or password.**". 

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_subtly_different_responses/assets/6.png)

Наше выделение появилось в данной панели.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_subtly_different_responses/assets/7.png)

Выбираем нужный словарь и начинаем атаку. Сортируем ответы по нашей строке. Нам приходит единственный ответ без точки в конце фразы.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_subtly_different_responses/assets/8.png)

Проделаем то же самое с паролем, только подставив уже известное нам имя пользователя.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_subtly_different_responses/assets/9.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_subtly_different_responses/assets/10.png)

Вводим логин и пароль и проходим аутентификацию. Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_subtly_different_responses/assets/11.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_subtly_different_responses/assets/12.png)
