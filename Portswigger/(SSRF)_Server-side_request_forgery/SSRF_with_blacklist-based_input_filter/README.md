# SSRF with blacklist-based input filter

## Theory

<h3>SSRF с входными фильтрами на основе черного списка</h3>

Некоторые приложения блокируют ввод, содержащий имена хостов, такие как 127.0.0.1 и localhost, или конфиденциальные URL-адреса, такие как /admin. В этой ситуации часто можно обойти фильтр, используя различные приемы:

* Использование альтернативного IP-представления 127.0.0.1, например 2130706433, 017700000001 или 127.1
* Регистрация собственного доменного имени с разрешением 127.0.0.1. Для этой цели вы можете использовать spoofed.burpcollaborator.net
* Обфускация заблокированных строк с использованием кодировки URL или варианта регистра.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_blacklist-based_input_filter/assets/1.png)

Выберем любой продукт.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_blacklist-based_input_filter/assets/2.png)

Перейдем на его страницу и спустимся в самый низ.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_blacklist-based_input_filter/assets/3.png)

Нажмем на кнопку "Check stock" и перехватим запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_blacklist-based_input_filter/assets/4.png)

Изменим stockApi на http://192.168.0.X/admin. Отправляем запроc. Мы можем заметить, что наш запрос был заблокирован системой безопасности.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_blacklist-based_input_filter/assets/5.png)

Для того, чтобы обойти блокировку мы будем использовать ip-адрес 127.1 и дважды закодируем первую букву в "admin".

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_blacklist-based_input_filter/assets/6.png)

Скопируем функцию удаления пользователя и вставим в stockApi. Отправляем запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_blacklist-based_input_filter/assets/7.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_blacklist-based_input_filter/assets/8.png)
