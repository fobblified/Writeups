# Information disclosure in error messages

## Theory

<h3>Сообщения об ошибках</h3>

Одной из наиболее распространенных причин раскрытия информации являются подробные сообщения об ошибках. Как правило, вы должны уделять пристальное внимание всем сообщениям об ошибках, с которыми вы сталкиваетесь во время аудита.

Содержимое сообщений об ошибках может раскрывать информацию о том, какой ввод или тип данных ожидается от данного параметра. Это может помочь вам сузить область атаки, определив параметры, которые можно использовать. Это может даже уберечь вас от траты времени на попытки внедрения полезной нагрузки, которая просто не будет работать.

Подробные сообщения об ошибках также могут предоставлять информацию о различных технологиях, используемых веб-сайтом. Например, они могут явно указать механизм шаблонов, тип базы данных или сервер, который использует веб-сайт, а также номер его версии. Эта информация может быть полезна, потому что вы можете легко найти любые задокументированные эксплойты, которые могут существовать для этой версии. Точно так же вы можете проверить, есть ли какие-либо распространенные ошибки конфигурации или опасные настройки по умолчанию, которые вы можете использовать. Некоторые из них могут быть выделены в официальной документации.

Вы также можете обнаружить, что веб-сайт использует какую-то платформу с открытым исходным кодом. В этом случае вы можете изучить общедоступный исходный код, который является бесценным ресурсом для создания собственных эксплойтов.

Различия между сообщениями об ошибках также могут свидетельствовать о различном поведении приложений, происходящем за кулисами. Наблюдение за различиями в сообщениях об ошибках является важным аспектом многих методов, таких как внедрение SQL, перечисление имен пользователей и т. д.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_in_error_messages/assets/1.png)

Перейдем на страницу любого из продуктов.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_in_error_messages/assets/2.png)

Найдем POST запрос /product?productid=2 - запрос нашего выбранного продукта.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_in_error_messages/assets/3.png)

Отравляем запрос в Repeater. Изменим значение картинки с цифры на случайный текст. Отправим запрос. Вызвав ошибку, мы можем заметить версию Apache.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_in_error_messages/assets/4.png)

Переходим к подтверждению ответа.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_in_error_messages/assets/5.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_in_error_messages/assets/6.png)
