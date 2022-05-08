# SQL injection UNION attack, retrieving data from other tables

## Theory

<h3>Использование UNION-атаки для извлечения интересных данных</h3>

Когда мы определили количество столбцов, возвращаемых исходным запросом, и нашли, какие столбцы могут содержать строковые данные, можно получить интересные данные.

Предположим, что:

* Исходный запрос возвращает два столбца, оба из которых могут содержать строковые данные.
* Точка внедрения — это строка в кавычках в WHERE.
* База данных содержит таблицу пользователей со столбцами имя пользователя и пароль.

В этой ситуации можно получить содержимое таблицы пользователей, отправив запрос:
```
' UNION SELECT username, password FROM users--
```

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_retrieving_data_from_other_tables/assets/1.png)

Выбираем пункт Lifestyle. Определяем [количество стобцов](https://github.com/fobblified/Writeups/tree/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_determining_the_number_of_columns_returned_by_the_query) и какой [тип данных](https://github.com/fobblified/Writeups/tree/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_finding_a_column_containing_text) они принимают. Вытаскиваем имена пользователей и пароли и таблицы.
```
/filter?category=AccessoriesUNION+SELECT+username,password+FROM+users--
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_retrieving_data_from_other_tables/assets/2.png)

Отправляем запрос и логинимся под адмнистратора.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_retrieving_data_from_other_tables/assets/3.png)
