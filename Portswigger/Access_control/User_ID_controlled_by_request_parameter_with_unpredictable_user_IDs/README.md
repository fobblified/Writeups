# User ID controlled by request parameter, with unpredictable user IDs

## Theory

<h3>Горизонтальное повышение привилегий</h3>

Горизонтальное повышение привилегий возникает, когда пользователь может получить доступ к ресурсам, принадлежащим другому пользователю, вместо своих собственных ресурсов этого типа. Например, если сотрудник должен иметь доступ только к своим записям о занятости и заработной плате, но на самом деле может также получить доступ к записям других сотрудников, то это горизонтальное повышение привилегий.

Атаки с горизонтальным повышением привилегий могут использовать те же типы методов эксплойта, что и вертикальное повышение привилегий. Например, обычно пользователь может получить доступ к странице своей учетной записи, используя URL-адрес, подобный следующему:
```
https://insecure-website.com/myaccount?id=123
```

Теперь, если злоумышленник изменит значение параметра id на значение другого пользователя, он может получить доступ к странице учетной записи другого пользователя с соответствующими данными и функциями.

В некоторых приложениях эксплуатируемый параметр не имеет предсказуемого значения. Например, вместо возрастающего числа приложение может использовать глобальные уникальные идентификаторы (GUID) для идентификации пользователей. Здесь злоумышленник может быть не в состоянии угадать или предсказать идентификатор другого пользователя. Однако идентификаторы GUID, принадлежащие другим пользователям, могут быть раскрыты в другом месте приложения, где упоминаются пользователи, например, в пользовательских сообщениях или отзывах.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_unpredictable_user_IDs/assets/1.png)

Логинимся с имеющимися данными. Найдем пост, который был создан пользователем carlos.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_unpredictable_user_IDs/assets/2.png)

Перейдем на его страницу. В поисковой строке скопируем id пользователя.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_unpredictable_user_IDs/assets/3.png)

Перейдем на страницу /my-account и перехватим запрос. Вместо своего id впишем id пользователя carlos.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_unpredictable_user_IDs/assets/4.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_unpredictable_user_IDs/assets/5.png)

Отправляем запрос. Теперь мы имеем доступ к странице пользователя carlos. Копируем ключ API.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_unpredictable_user_IDs/assets/6.png)

Переходим к подтвеждению ответа.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_unpredictable_user_IDs/assets/7.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/User_ID_controlled_by_request_parameter_with_unpredictable_user_IDs/assets/8.png)
