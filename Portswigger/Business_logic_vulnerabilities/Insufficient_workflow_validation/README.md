# Insufficient workflow validation

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

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Insufficient_workflow_validation/assets/1.png)

Логинимся с имеющимися данными.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Insufficient_workflow_validation/assets/2.png)

Переходим на гланую страницу и добавляем в корзину случайный продукт.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Insufficient_workflow_validation/assets/3.png)

Размещаем заказ.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Insufficient_workflow_validation/assets/4.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Insufficient_workflow_validation/assets/5.png)

Находим GET запрос /cart/order-confirmation... Отпраляем его в Repeater.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Insufficient_workflow_validation/assets/6.png)

Переходим на гланую страницу и добавляем в корзину необходимый продукт.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Insufficient_workflow_validation/assets/7.png)

Отправляем запрос, который находится в Repeater. Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Insufficient_workflow_validation/assets/8.png)
