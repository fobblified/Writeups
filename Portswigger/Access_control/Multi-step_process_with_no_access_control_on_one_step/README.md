# Multi-step process with no access control on one step

## Theory

<h3>Уязвимости контроля доступа в многоэтапных процессах</h3>

Многие веб-сайты реализуют важные функции в несколько этапов. Это часто делается, когда необходимо зафиксировать различные входные данные или параметры, или когда пользователю необходимо просмотреть и подтвердить детали перед выполнением действия. Например, административная функция для обновления сведений о пользователе может включать следующие шаги:
1. Загрузить форму, содержащую данные для конкретного пользователя
2. Отправить изменения
3. Просмотрите изменения и подтвердите

Иногда веб-сайт реализует строгий контроль доступа к некоторым из этих шагов, но игнорирует другие. Например, предположим, что управление доступом правильно применяется к первому и второму шагам, но не к третьему шагу. По сути, веб-сайт предполагает, что пользователь достигнет шага 3 только в том случае, если он уже выполнил первые шаги, которые должным образом контролируются. Здесь злоумышленник может получить несанкционированный доступ к функции, пропустив первые два шага и напрямую отправив запрос на третий шаг с требуемыми параметрами.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter
    * Данные администратора: administrator:admin

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Multi-step_process_with_no_access_control_on_one_step/assets/1.png)

Логинимся под администратора.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Multi-step_process_with_no_access_control_on_one_step/assets/2.png)

У нас есть доступ к панели управления привилегиями.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Multi-step_process_with_no_access_control_on_one_step/assets/3.png)

Повышаем привилегии пользователя. Теперь нас просят подтвердить изменения.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Multi-step_process_with_no_access_control_on_one_step/assets/4.png)

Перехватываем запрос и отправляем его в Repeater.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Multi-step_process_with_no_access_control_on_one_step/assets/5.png)

Зайдем на этот же сайт во вкладке инкогнито и залогинимся под пользователя wiener.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Multi-step_process_with_no_access_control_on_one_step/assets/6.png)

Обновляем страницу и перехватываем запрос. Скопируем значение параметра session.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Multi-step_process_with_no_access_control_on_one_step/assets/7.png)

Изменим значение сессии и имя пользователя в запросе, который мы отправили в Repeater. Отправляем запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Multi-step_process_with_no_access_control_on_one_step/assets/8.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Multi-step_process_with_no_access_control_on_one_step/assets/9.png)
