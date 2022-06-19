# Authentication bypass via flawed state machine

## Theory

<h3>Пользователи не всегда будут следовать намеченной последовательности</h3>

Многие транзакции основаны на предопределенных рабочих процессах, состоящих из последовательности шагов. Веб-интерфейс обычно направляет пользователей через этот процесс, переводя их на следующий этап рабочего процесса каждый раз, когда они завершают текущий. Однако злоумышленники не обязательно будут придерживаться этой предполагаемой последовательности. Неучет этой возможности может привести к опасным недостаткам, которые относительно легко использовать.

Предположения о последовательности событий могут привести к большому количеству проблем даже в рамках одного и того же рабочего процесса или функциональности. Используя такие инструменты, как Burp Proxy и Repeater, как только злоумышленник увидит запрос, он может воспроизвести его по своему желанию и использовать принудительный просмотр для выполнения любых взаимодействий с сервером в любом порядке, в котором он хочет. Это позволяет им выполнять различные действия, пока приложение находится в непредвиденном состоянии.

Чтобы выявить такие недостатки, вы должны использовать принудительный просмотр для отправки запросов в непреднамеренной последовательности. Например, вы можете пропустить определенные шаги, перейти к одному шагу более одного раза, вернуться к более ранним шагам и т. д. Обратите внимание на то, как осуществляется доступ к различным шагам. Хотя вы часто просто отправляете запрос GET или POST на определенный URL-адрес, иногда вы можете получить доступ к шагам, отправив разные наборы параметров на один и тот же URL-адрес. Как и в случае со всеми логическими ошибками, попытайтесь определить, какие предположения сделали разработчики и где лежит поверхность атаки. Затем вы можете искать способы нарушения этих предположений.

Обратите внимание, что такое тестирование часто вызывает исключения, поскольку ожидаемые переменные имеют нулевые или неинициализированные значения. Прибытие в местоположение в частично определенном или несогласованном состоянии также может вызвать жалобу приложения. В этом случае внимательно следите за любыми сообщениями об ошибках или отладочной информацией, с которыми вы сталкиваетесь. Они могут быть ценным источником раскрытия информации, который может помочь вам точно настроить атаку и понять ключевые детали поведения серверной части.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_flawed_state_machine/assets/1.png)

Перейдем в меню Site map. Выбираем наш сайт, открываем выпадаеющее меню. Находим функцию Engagement tools -> Discover content.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_flawed_state_machine/assets/2.png)

Запускаем сканирование.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_flawed_state_machine/assets/3.png)

Находим страницу /admin. 

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_flawed_state_machine/assets/4.png)

Переходим на страницу /admin. Теперь мы знаем, что пользователя с права админа зовут administrator.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_flawed_state_machine/assets/5.png)

Запускаем перехват запросов и логинимся с имеющимися данными. Пропускаем все запросы, останавливаемся на /role-selector. 

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_flawed_state_machine/assets/6.png)

Дропаем этот запрос. В адресной строке удаляем /role-selector. 

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_flawed_state_machine/assets/7.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_flawed_state_machine/assets/8.png)

Получаем доступ администратора.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_flawed_state_machine/assets/9.png)

Переходим в панель админа.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_flawed_state_machine/assets/10.png)

Удаляем пользователя carlos и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_flawed_state_machine/assets/11.png)
