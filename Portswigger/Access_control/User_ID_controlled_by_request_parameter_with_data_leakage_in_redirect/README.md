# User ID controlled by request parameter with data leakage in redirect

## Theory

<h3>Горизонтальное повышение привилегий</h3>

Горизонтальное повышение привилегий возникает, когда пользователь может получить доступ к ресурсам, принадлежащим другому пользователю, вместо своих собственных ресурсов этого типа. Например, если сотрудник должен иметь доступ только к своим записям о занятости и заработной плате, но на самом деле может также получить доступ к записям других сотрудников, то это горизонтальное повышение привилегий.

Атаки с горизонтальным повышением привилегий могут использовать те же типы методов эксплойта, что и вертикальное повышение привилегий. Например, обычно пользователь может получить доступ к странице своей учетной записи, используя URL-адрес, подобный следующему:
```
https://insecure-website.com/myaccount?id=123
```

Теперь, если злоумышленник изменит значение параметра id на значение другого пользователя, он может получить доступ к странице учетной записи другого пользователя с соответствующими данными и функциями.

В некоторых приложениях эксплуатируемый параметр не имеет предсказуемого значения. Например, вместо возрастающего числа приложение может использовать глобальные уникальные идентификаторы (GUID) для идентификации пользователей. Здесь злоумышленник может быть не в состоянии угадать или предсказать идентификатор другого пользователя. Однако идентификаторы GUID, принадлежащие другим пользователям, могут быть раскрыты в другом месте приложения, где упоминаются пользователи, например, в пользовательских сообщениях или отзывах.

В некоторых случаях приложение обнаруживает, что пользователю запрещен доступ к ресурсу, и возвращает перенаправление на страницу входа. Однако ответ, содержащий перенаправление, может по-прежнему содержать некоторые конфиденциальные данные, принадлежащие целевому пользователю, поэтому атака все равно будет успешной.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_data_leakage_in_redirect/assets/1.png)

Логинимся с имеющимися данными.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_data_leakage_in_redirect/assets/2.png)

Обновим страницу и перехватим запрос. Отправив этот же запрос, мы можем увидеть, что нам приходит ответ, в котором отображено все содержимое страницы, включаю ключ API.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_data_leakage_in_redirect/assets/3.png)

Изменим id пользователя на carlos и отправим запрос. Нам так же пришел ответ с содержимым страницы. Скопируем ключ API.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_data_leakage_in_redirect/assets/4.png)

Переходим к подтвеждению ответа.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_data_leakage_in_redirect/assets/5.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_data_leakage_in_redirect/assets/6.png)
