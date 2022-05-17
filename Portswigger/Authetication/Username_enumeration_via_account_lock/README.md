# Username enumeration via account lock

## Theory

<h3>Блокировка аккаунта</h3>

Один из способов, с помощью которого веб-сайты пытаются предотвратить взлом, — это блокировка учетной записи, если выполняются определенные подозрительные критерии, обычно определенное количество неудачных попыток входа в систему. Как и в случае с обычными ошибками входа в систему, ответы сервера, указывающие на то, что учетная запись заблокирована, также могут помочь злоумышленнику перечислить имена пользователей.

Блокировка учетной записи обеспечивает определенную степень защиты от целенаправленного взлома определенной учетной записи. Однако этот подход не может адекватно предотвратить атаки грубой силы, в которых злоумышленник просто пытается получить доступ к любой случайной учетной записи.

Например, для обхода такого рода защиты можно использовать следующий метод:

* Составьте список потенциальных имен пользователей, которые, вероятно, будут действительными. Это может быть путем перечисления имен пользователей или просто на основе списка общих имен пользователей.
* Однако этот подход не может адекватно предотвратить атаки грубой силы, в которых злоумышленник просто пытается получить доступ к любой случайной учетной записи, которую он может. Важно отметить, что количество выбранных вами паролей не должно превышать количество разрешенных попыток входа в систему. Например, если вы определили, что ограничение составляет 3 попытки, вам нужно выбрать максимум 3 угадывания пароля.
* Используя такой инструмент, как Burp Intruder, попробуйте каждый из выбранных паролей с каждым из возможных имен пользователей. Таким образом, вы можете попытаться взломать каждую учетную запись без блокировки учетной записи. Вам нужен только один пользователь, чтобы использовать один из трех паролей, чтобы скомпрометировать учетную запись.

Блокировка учетной записи также не защищает от атак с подменой учетных данных. Это включает в себя использование огромного словаря пар имя пользователя: пароль, состоящего из подлинных учетных данных для входа, украденных при утечке данных. Заполнение учетных данных основано на том факте, что многие люди повторно используют одно и то же имя пользователя и пароль на нескольких веб-сайтах, и, следовательно, есть вероятность, что некоторые из скомпрометированных учетных данных в словаре также действительны на целевом веб-сайте. Блокировка учетной записи не защищает от заполнения учетных данных, поскольку каждое имя пользователя используется только один раз. Вброс учетных данных особенно опасен, потому что иногда злоумышленник может скомпрометировать множество разных учетных записей с помощью всего одной автоматической атаки. 

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_account_lock/assets/1.png)

Страница аутентификации:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_account_lock/assets/2.png)

Введем случайное значение имени пользователя и пароля. В ответ мы получаем строку "Invalid username or password".

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_account_lock/assets/3.png)

Отправим запрос в Burp Intruder. Вставим имя пользователя между специальными символами, а так же добавим эти символы после пароля.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_account_lock/assets/4.png)

Вместо словаря для второго значения перебора будем подставлять пустные значения.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_account_lock/assets/5.png)

Это приведет к тому, что каждое имя пользователя будет повторяться 5 раз. В результатах обратим внимание, что ответы для одного из имен пользователей были длиннее, чем ответы при использовании других имен пользователей. В ответ мы получим сообщение о том, что мы предприняли слишком много неправильных попыток входа в систему, значит имя пользователя есть в системе.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_account_lock/assets/6.png)

Подставим имя пользователя и вставим пароль в специальные символы.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_account_lock/assets/7.png)

Зайдем во вкладку options Burp Intruder-а и найдем панель **Grep - Extract**. Нажмем на кнопку **Add**, чтобы добавить поиск по фразе.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_account_lock/assets/8.png)

Выделим ответ от сервера, а именно **"Invalid username or password."**.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_account_lock/assets/9.png)

Наше выделение появилось в данной панели.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_account_lock/assets/10.png)

Выбираем нужный словарь и начинаем атаку. Сортируем ответы по нашей строке. Нам приходит единственный ответ без какой-либо фразы.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_account_lock/assets/11.png)

Вводим логин и пароль и проходим аутентификацию. Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_account_lock/assets/12.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Username_enumeration_via_account_lock/assets/13.png)
