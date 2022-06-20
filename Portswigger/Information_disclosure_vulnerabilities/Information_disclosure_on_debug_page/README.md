# Information disclosure on debug page

## Theory

<h3>Отладочные данные</h3>

В целях отладки многие веб-сайты создают собственные сообщения об ошибках и журналы, содержащие большое количество информации о поведении приложения. Хотя эта информация полезна во время разработки, она также чрезвычайно полезна для злоумышленника, если она просочится в производственной среде.

Отладочные сообщения иногда могут содержать жизненно важную информацию для разработки атаки, в том числе:

* Значения ключевых переменных сеанса, которыми можно управлять с помощью пользовательского ввода
* Имена хостов и учетные данные для серверных компонентов
* Имена файлов и каталогов на сервере
* Ключи, используемые для шифрования данных, передаваемых через клиент

Отладочная информация может иногда записываться в отдельный файл. Если злоумышленник сможет получить доступ к этому файлу, он может послужить полезной ссылкой для понимания состояния выполнения приложения. Это также может дать несколько подсказок относительно того, как они могут предоставлять созданные входные данные для управления состоянием приложения и контроля полученной информации.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_on_debug_page/assets/1.png)

Перейдем в меню Site map. Выбираем наш сайт, открываем выпадаеющее меню. Находим функцию Engagement tools -> Find comments.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_on_debug_page/assets/2.png)

Можем заметить, что домашняя страница содержит комментарий HTML со ссылкой «Debug».

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_on_debug_page/assets/3.png)

Найдем этот файл в меню Site map. Отправляем запрос в Repeater.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_on_debug_page/assets/4.png)

В Repeater отправляем запрос и получаем SECRET_KEY.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_on_debug_page/assets/5.png)

Переходим к подтверждению ответа.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_on_debug_page/assets/6.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_on_debug_page/assets/7.png)
