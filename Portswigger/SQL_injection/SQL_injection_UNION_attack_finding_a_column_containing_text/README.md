# SQL injection UNION attack, finding a column containing text

## Theory

<h3>Поиск столбцов с полезным типом данных в атаке UNION</h3>

Причина выполнения атаки UNION с внедрением SQL-кода заключается в том, чтобы иметь возможность получить результаты из внедренного запроса. Как правило, интересные данные, которые мы хотим получить, будут в форме строки, поэтому нужно найти один или несколько столбцов в исходных результатах запроса, тип данных которых является или совместим со строковыми данными.

Уже определив количество необходимых столбцов, можно протестировать каждый столбец, чтобы проверить, может ли он содержать строковые данные, отправив серию полезных данных UNION SELECT, которые помещают строковое значение в каждый столбец по очереди.

Например, если запрос возвращает четыре столбца, нужно отправить:
```
' UNION SELECT 'a',NULL,NULL,NULL--
' UNION SELECT NULL,'a',NULL,NULL--
' UNION SELECT NULL,NULL,'a',NULL--
' UNION SELECT NULL,NULL,NULL,'a'--
```

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_finding_a_column_containing_text/assets/1.png)

Выбираем пункт Lifestyle. Запрос:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_finding_a_column_containing_text/assets/2.png)

Чтобы определить количество столбцов, будем подставлять NULL значения. Выясняем, что их 3.
```
/filter?category=Lifestyle'+UNION+SELECT+NULL,NULL,NULL--
```
![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_finding_a_column_containing_text/assets/3.png)

Проверяем, может ли столбец содержать строковые данные. Выясняем, что второй столбец может их содерать.
```
/filter?category=Lifestyle'+UNION+SELECT+NULL,'a',NULL--
```
![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_finding_a_column_containing_text/assets/4.png)

Подставляем значение, нужное для решения задачи.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_finding_a_column_containing_text/assets/5.png)


![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/SQL_injection_UNION_attack_finding_a_column_containing_text/assets/6.png)
