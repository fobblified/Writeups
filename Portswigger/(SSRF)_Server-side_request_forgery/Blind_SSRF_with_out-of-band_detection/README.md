# Blind SSRF with out-of-band detection

## Theory

<h3>Как найти и использовать слепые SSRF-уязвимости</h3>

Самый надежный способ обнаружения слепых SSRF-уязвимостей — использование внеполосных (OAST) методов. Это включает в себя попытку инициировать HTTP-запрос к внешней системе, которую вы контролируете, и отслеживание сетевых взаимодействий с этой системой.

Самый простой и эффективный способ использовать внеполосные методы — использовать Burp Collaborator. Вы можете использовать клиент Burp Collaborator для создания уникальных доменных имен, отправки их в виде полезной нагрузки в приложение и отслеживания любого взаимодействия с этими доменами. Если наблюдается входящий HTTP-запрос, исходящий от приложения, то оно уязвимо для SSRF.

Обычно при тестировании на уязвимости SSRF наблюдается поиск DNS для предоставленного домена Collaborator, но не последующий HTTP-запрос. Обычно это происходит из-за того, что приложение пыталось выполнить HTTP-запрос к домену, что вызвало первоначальный поиск DNS, но фактический HTTP-запрос был заблокирован фильтрацией на уровне сети. Инфраструктура довольно часто разрешает исходящий трафик DNS, поскольку он необходим для очень многих целей, но блокирует HTTP-соединения с неожиданными пунктами назначения.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Blind_SSRF_with_out-of-band_detection/assets/1.png)

Выберем любой продукт.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Blind_SSRF_with_out-of-band_detection/assets/2.png)

Перехватим запрос и отправим его в Repeater.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Blind_SSRF_with_out-of-band_detection/assets/3.png)

Откроем Burp Collaborator client и скопируем домен.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Blind_SSRF_with_out-of-band_detection/assets/4.png)

В заголовке Referer укажем наш домен. Отправляем запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Blind_SSRF_with_out-of-band_detection/assets/5.png)

В Burp Collaborator client мы можем заметить взаимодействие с нашим DNS.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Blind_SSRF_with_out-of-band_detection/assets/6.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Blind_SSRF_with_out-of-band_detection/assets/7.png)
