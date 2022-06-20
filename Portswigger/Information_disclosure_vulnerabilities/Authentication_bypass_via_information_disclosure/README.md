# Authentication bypass via information disclosure

## Theory

<h3>Раскрытие информации из-за небезопасной конфигурации</h3>

Веб-сайты иногда уязвимы из-за неправильной настройки. Это особенно распространено из-за широкого использования сторонних технологий, широкий набор параметров конфигурации которых не всегда хорошо понимается теми, кто их внедряет.

В других случаях разработчики могут забыть отключить различные параметры отладки в рабочей среде. Например, метод HTTP TRACE предназначен для диагностических целей. Если этот параметр включен, веб-сервер будет отвечать на запросы, использующие метод TRACE, повторяя в ответе точный полученный запрос. Такое поведение часто безвредно, но иногда приводит к раскрытию информации, такой как имена заголовков внутренней аутентификации, которые могут добавляться к запросам обратными прокси-серверами.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Authentication_bypass_via_information_disclosure/assets/1.png)

Логинимся с имеющимися данными.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Authentication_bypass_via_information_disclosure/assets/2.png)

Проверив страницу /admin, мы можем заметить следующее сообщение.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Authentication_bypass_via_information_disclosure/assets/3.png)

Найдем GET запрос /admin. Отправим его в Repeater.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Authentication_bypass_via_information_disclosure/assets/4.png)

Изменим метод GET на метод TRACE и отправим запрос. Мы можем заметить, что заоловок **X-Custom-IP-Authorization**, содержащий наш IP-адрес, был автоматически добавлен к запросу. Этот заголовок используется для определения того, пришел ли запрос с IP-адреса локального хоста.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Authentication_bypass_via_information_disclosure/assets/5.png)

Перейдем в меню Proxy -> Options -> Match and Replace. Добавляем новый параметр, чтобы ко всем нашим запросам автоматически подставлялся нужный заголовок.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Authentication_bypass_via_information_disclosure/assets/6.png)

Вставляем нужный нам заголовок и вместо нашего Ip-адреса пропишем локальный.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Authentication_bypass_via_information_disclosure/assets/7.png)

Переходим на домашнюю страницу. Теперь мы имеем доступ к админ панели.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Authentication_bypass_via_information_disclosure/assets/8.png)

Переходим в админ панель и удаляем пользователя carlos.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Authentication_bypass_via_information_disclosure/assets/9.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Authentication_bypass_via_information_disclosure/assets/10.png)
