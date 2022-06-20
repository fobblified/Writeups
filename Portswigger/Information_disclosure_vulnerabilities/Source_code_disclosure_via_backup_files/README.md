# Source code disclosure via backup files

## Theory

<h3>Раскрытие исходного кода через файлы резервных копий</h3>

Получение доступа к исходному коду значительно облегчает злоумышленнику понимание поведения приложения и создание атак высокой степени серьезности. Конфиденциальные данные иногда даже жестко закодированы в исходном коде. Типичные примеры этого включают ключи API и учетные данные для доступа к внутренним компонентам.

Если вы можете определить, что используется конкретная технология с открытым исходным кодом, это обеспечивает легкий доступ к ограниченному количеству исходного кода.

Иногда даже можно заставить веб-сайт раскрывать свой собственный исходный код. При отображении веб-сайта вы можете обнаружить, что на некоторые файлы исходного кода имеются явные ссылки. К сожалению, их запрос обычно не раскрывает сам код. Когда сервер обрабатывает файлы с определенным расширением, например .php, он обычно выполняет код, а не просто отправляет его клиенту в виде текста. Однако в некоторых ситуациях вы можете обманом заставить веб-сайт вернуть содержимое файла. Например, текстовые редакторы часто создают временные файлы резервных копий, пока исходный файл редактируется. Эти временные файлы обычно обозначаются каким-либо образом, например добавлением тильды (~) к имени файла или добавлением другого расширения файла. Запрос файла кода с использованием расширения файла резервной копии иногда позволяет прочитать содержимое файла в ответе.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Source_code_disclosure_via_backup_files/assets/1.png)

Проверим есть ли файл robots.txt.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Source_code_disclosure_via_backup_files/assets/2.png)

Такой файл существуем и в нем находится директория /backup. Перейдем в меню Site map и отправим GET запрос /backup в Repeater.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Source_code_disclosure_via_backup_files/assets/3.png)

Отправляем запрос и получаем название другого файла.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Source_code_disclosure_via_backup_files/assets/4.png)

Попробуем его открыть. Изменяем запрос и отправляем его. Получаем пароль базы данных.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Source_code_disclosure_via_backup_files/assets/5.png)

Переходим к подтверждению ответа.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Source_code_disclosure_via_backup_files/assets/6.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Source_code_disclosure_via_backup_files/assets/7.png)
