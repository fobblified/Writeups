# Inconsistent security controls

## Theory

<h3>Ошибочные предположения о поведении пользователей</h3>

Одной из наиболее распространенных причин логических уязвимостей являются ошибочные предположения о поведении пользователя. Это может привести к широкому кругу проблем, когда разработчики не учли потенциально опасные сценарии, нарушающие эти предположения.

<h3>Доверенные пользователи не всегда заслуживают доверие</h3>

Приложения могут казаться безопасными, потому что они реализуют кажущиеся надежными меры по обеспечению соблюдения бизнес-правил. К сожалению, некоторые приложения допускают ошибку, полагая, что, пройдя этот строгий контроль изначально, пользователю и его данным можно доверять бесконечно. Это может привести к относительно слабому применению одних и тех же средств контроля с этого момента.

Если бизнес-правила и меры безопасности не применяются последовательно во всем приложении, это может привести к потенциально опасным лазейкам, которыми может воспользоваться злоумышленник.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_security_controls/assets/1.png)

Перейдем в меню Site map. Выбираем наш сайт, открываем выпадаеющее меню. Находим функцию Engagement tools -> Discover content.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_security_controls/assets/2.png)

Запускаем сканирование.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_security_controls/assets/3.png)

Находим страницу /admin.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_security_controls/assets/4.png)

Перейдем к этой странице. На ней можно увидеть, что только пользователь DontWannaCry имеет доступ к админ панели.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_security_controls/assets/5.png)

Попробуем создать нового пользователя. Перейдем на наш Email client и скопируем адрес.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_security_controls/assets/6.png)

Перейдем на страницу решистрации. Введем случайные имя пользователя и пароль. В строке email введем наш скопированный адрес.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_security_controls/assets/7.png)

Регистрируем пользователя и потверждаем регистрацию на странице Email client.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_security_controls/assets/8.png)


Логинимся под нового пользователя. Мы можем заметить панель обновления почты пользователя.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_security_controls/assets/9.png)

Введем адрес почты админа.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_security_controls/assets/10.png)

Обновляем адрес почты и получаем доступ к панели админа.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_security_controls/assets/11.png)

Переходим в админ панель и удаляем пользователя carlos.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_security_controls/assets/12.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_security_controls/assets/13.png)
