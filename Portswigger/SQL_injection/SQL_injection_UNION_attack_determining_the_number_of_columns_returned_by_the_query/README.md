# SQL injection UNION attack, determining the number of columns returned by the query

## Theory

<h3>UNION-атаки</h3>

Когда приложение уязвимо для SQL-инъекций и результаты запроса возвращаются в ответах приложения, можно использовать ключевое слово UNION для извлечения данных из других таблиц в базе данных. Это приводит к атаке UNION с внедрением SQL.

Ключевое слово UNION позволяет выполнить один или несколько дополнительных запросов SELECT и добавить результаты к исходному запросу.
```
SELECT a, b FROM table1 UNION SELECT c, d FROM table2
```

Этот SQL-запрос вернет один результирующий набор с двумя столбцами, содержащими значения из столбцов a и b в таблице1 и столбцов c и d в таблице2.

Чтобы запрос UNION работал, должны быть выполнены два ключевых требования:

* Отдельные запросы должны возвращать одинаковое количество столбцов.
* Типы данных в каждом столбце должны быть совместимы между отдельными запросами.

Чтобы выполнить атаку UNION с внедрением SQL-кода, вам необходимо убедиться, что ваша атака соответствует этим двум требованиям.

Обычно это включает в себя выяснение:

* Сколько столбцов возвращается из исходного запроса?
* Какие столбцы, возвращенные исходным запросом, имеют подходящий тип данных для хранения результатов введенного запроса?

<br></br>

<h3>Определение количества столбцов, необходимых для атаки UNION</h3>

Cуществует два эффективных метода определения количества столбцов, возвращаемых исходным запросом.

Первый метод включает в себя вставку ряда предложений ORDER BY и увеличение указанного индекса столбца до тех пор, пока не произойдет ошибка.

Например, если предположить, что точка внедрения — это строка в кавычках в предложении WHERE исходного запроса, вы должны отправить:
```
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
...
```

Когда указанный индекс столбца превышает количество фактических столбцов в результирующем наборе, база данных возвращает ошибку.

Второй метод включает в себя отправку серии данных UNION SELECT, указывающих различное количество нулевых значений:
```
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
...
```

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_determining_the_number_of_columns_returned_by_the_query/assets/1.png)

Выбираем пункт Lifestyle. Запрос:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_determining_the_number_of_columns_returned_by_the_query/assets/2.png)

Чтобы определить количество столбцов, будем подставлять NULL значения. Получаем ошибку - идем дальше.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_determining_the_number_of_columns_returned_by_the_query/assets/3.png)

При подстановке третьего NULL приложение перестает выдавать ошибку.
```
/filter?=category=Lifestyle'+UNION+SELECT+NULL,NULL,NULL--
```
![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_determining_the_number_of_columns_returned_by_the_query/assets/4.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_determining_the_number_of_columns_returned_by_the_query/assets/5.png)

