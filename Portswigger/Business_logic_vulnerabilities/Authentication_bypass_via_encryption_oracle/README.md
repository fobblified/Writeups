# Authentication bypass via encryption oracle

## Theory

<h3>Предоставление оракула шифрования</h3>

Опасные сценарии могут возникать, когда входные данные, контролируемые пользователем, шифруются, а полученный зашифрованный текст затем каким-то образом становится доступным для пользователя. Этот тип ввода иногда называют «оракулом шифрования». Злоумышленник может использовать этот ввод для шифрования произвольных данных с использованием правильного алгоритма и асимметричного ключа.

Это становится опасным, когда в приложении есть другие входные данные, контролируемые пользователем, которые ожидают данные, зашифрованные с помощью того же алгоритма. В этом случае злоумышленник потенциально может использовать оракул шифрования для создания действительных зашифрованных входных данных, а затем передать их другим конфиденциальным функциям.

Эта проблема может усугубиться, если на сайте есть другой вход, управляемый пользователем, который обеспечивает обратную функцию. Это позволит злоумышленнику расшифровать другие данные, чтобы идентифицировать ожидаемую структуру.Это избавляет их от части работы, связанной с созданием их вредоносных данных, но не обязательно требуется для создания успешного эксплойта.

Серьезность оракула шифрования зависит от того, какие функции также используют тот же алгоритм, что и оракул.

(Оракул — это любая система, которая может дать некоторую дополнительную информацию о системе, которая в противном случае была бы недоступна.)

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/1.png)

Логинимся с имеющимися данными, не забывая нажать на кнопку "Stay logged in".

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/2.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/3.png)

Переходим на главную страницу, а затем к одному из постов.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/4.png)

Переходим в низ страницы и видим поле для комментария. Оставляем комментарий со случайными данными.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/5.png)

Теперь мы можем уидеть свой комментарий на этой странице.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/6.png)

Теперь попробуем оставить комментарий введя неправильный адрес почты.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/7.png)

В начале страницы можем заметить сообщение об ошибке. Когда мы пытаемся отправить комментарий, используя недействительный адрес электронной почты, ответ устанавливает зашифрованный файл cookie уведомления, прежде чем перенаправить нас на запись в блоге.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/8.png)

Перейдем в историю запросов и найдем два запроса. POST запрос /post/comment и GET запрос /post?postid=3. Отправляем из в Repeater.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/9.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/10.png)

Скопируем параметр stay-logged-in из первого запроса и вставим его в параметр notification второго запроса. Отправляем запрос и видим, что вместо сообщения об ошибке мы получаем расшифрованный файл cookie. Скопируем временную метку.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/11.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/12.png)

Вернемся к первому запросу, который кодирует данные введем имя администратора и временную метку по предыдущему примеру. Отправляем запрос. Скопируем параметр notification.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/13.png)

Вставим скопированный параметр во второй запрос в параметр notification для декодирования данных. Получаем сообщение о том что введенный адрес неверный.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/14.png)

Посчитаем количество символов в сообщении об ошибке без учета данных имени пользователя и временной метки. Количество символов равно 23. Снова скопируем параметр notification. Перейдем в Decoder и вставим скопированный параметр. Декодируем как URL -> Base64, удаляем первые 23 символа и снова кодируем как Base64 -> URL.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/15.png)

Копируем новый параметр и вставляем в запрос для декодирования. Теперь мы получаем сообщение о том, что входная длина должна быть кратна 16. 

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/16.png)

Перейдем в запрос для шифрования и добавим 9 символов, чтобы предотвратить ошибку(23 символа + 9 символов = 32). Отправляем запрос. Копируем параметр.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/17.png)

Снова перейдем в Decoder и вставляем скопированный параметр. Декодируем как URL -> Base64, удаляем первые 32 символа и снова кодируем как Base64 -> URL.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/18.png)

Копируем параметр и переходим в запрос для декодирования. Вставляем его и отправляем запрос. Теперь данные передаются корректно.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/19.png)

Запускаем перехват запросов и переходим на домашнюю страницу.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/20.png)

Удаляем параметр session и изменяем параметр stay-logged-in на раннее созданный.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/21.png)

Отправляем запрос и получаем доступ к админ панели.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/22.png)

Переходим в панель админа и удаляем пользователя carlos.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/23.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Authentication_bypass_via_encryption_oracle/assets/24.png)
