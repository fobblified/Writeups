# Excessive trust in client-side controls

## Theory

<h3>Чрезмерное доверие к элементам управления на стороне клиента</h3>

Принципиально ошибочное предположение состоит в том, что пользователи будут взаимодействовать с приложением только через предоставленный веб-интерфейс. Это особенно опасно, поскольку приводит к дальнейшему предположению, что проверка на стороне клиента не позволит пользователям вводить вредоносные данные. Однако злоумышленник может просто использовать такие инструменты, как Burp Proxy, для подделки данных после того, как они были отправлены браузером, но до того, как они будут переданы в логику на стороне сервера.

Принятие данных за чистую монету без выполнения надлежащих проверок целостности и проверки на стороне сервера может позволить злоумышленнику нанести любой ущерб с относительно минимальными усилиями. Именно то, чего они могут достичь, зависит от функциональности и того, что они делают с контролируемыми данными. В правильном контексте такой недостаток может иметь разрушительные последствия как для функциональности, связанной с бизнесом, так и для безопасности самого веб-сайта.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Excessive_trust_in_client-side_controls/assets/1.png)

Перейдем на страницу аунтентификации.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Excessive_trust_in_client-side_controls/assets/2.png)

Логинимся с имеющимися данными.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Excessive_trust_in_client-side_controls/assets/3.png)

Перейдем на главную страницу.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Excessive_trust_in_client-side_controls/assets/4.png)

Выберем нужный нам продукт и перейдем в конец страницы.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Excessive_trust_in_client-side_controls/assets/5.png)

Нажмем на кнопку "Add to card", чтобы добавить продукт в корзину. Перехватим запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Excessive_trust_in_client-side_controls/assets/6.png)

Изменим стоимость продукта и отправим запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Excessive_trust_in_client-side_controls/assets/7.png)

Перейдем на страницу корзины с товарами.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Excessive_trust_in_client-side_controls/assets/8.png)

Нажимаем на кнопку "Place order".

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Excessive_trust_in_client-side_controls/assets/9.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Excessive_trust_in_client-side_controls/assets/10.png)
