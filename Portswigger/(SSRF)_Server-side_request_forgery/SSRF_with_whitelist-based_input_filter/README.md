# SSRF with whitelist-based input filter

## Theory

<h3>SSRF с входными фильтрами на основе белого списка</h3>

Некоторые приложения разрешают только ввод, который соответствует, начинается с или содержит белый список разрешенных значений. В этой ситуации вы иногда можете обойти фильтр, используя несоответствия при синтаксическом анализе URL.

Спецификация URL-адреса содержит ряд функций, которые могут быть упущены из виду при реализации специального синтаксического анализа и проверки URL-адресов:

* Вы можете вставить учетные данные в URL-адрес перед именем хоста, используя символ @. Например: 
```
https://expected-host@evil-host
```
* Вы можете использовать символ # для обозначения фрагмента URL. Например:
```
https://evil-host#expected-host
```
* Вы можете использовать иерархию DNS-имен, чтобы поместить необходимые входные данные в полное DNS-имя, которым вы управляете. Например:
```
https://expected-host.evil-host
```
* Вы можете закодировать символы URL, чтобы запутать код анализа URL. Это особенно полезно, если код, реализующий фильтр, обрабатывает символы в кодировке URL иначе, чем код, выполняющий серверный HTTP-запрос.
* Вы можете использовать комбинации этих методов вместе.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_whitelist-based_input_filter/assets/1.png)

Выберем любой продукт.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_whitelist-based_input_filter/assets/2.png)

Перейдем на его страницу и спустимся в самый низ.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_whitelist-based_input_filter/assets/3.png)

Нажмем на кнопку "Check stock" и перехватим запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_whitelist-based_input_filter/assets/4.png)

Раскодируем stockApi, чтобы видеть, из чего он состоит.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_whitelist-based_input_filter/assets/5.png)

Попробуем обратиться к localhost. Нас встречает сообщение о том, что мы должны обращаться к stock.weliketoshop.net.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_whitelist-based_input_filter/assets/6.png)

Попробуем добавить следующую конструкцию: http://username@stock.weliketoshop.net. Отправляем запрос и получаем ответ, значит такая конструкция является рабочей.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_whitelist-based_input_filter/assets/7.png)

Попробуем добавить # в запрос. Отправялем запрос. Мы получаем сообщение об ошибке.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_whitelist-based_input_filter/assets/8.png)

Чтобы обойти это, дважды закодируем символ "#". Отправляем запрос и получаем ответ.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_whitelist-based_input_filter/assets/9.png)

Теперь вместо username мы будем обращаться к localhost:80.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_whitelist-based_input_filter/assets/10.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_whitelist-based_input_filter/assets/11.png)

Скопируем функцию удаления пользователя и вставим в stockApi. Отправляем запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_whitelist-based_input_filter/assets/12.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/SSRF_with_whitelist-based_input_filter/assets/13.png)
