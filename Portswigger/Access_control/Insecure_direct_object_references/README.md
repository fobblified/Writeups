# Insecure direct object references

## Theory

<h3>Небезопасные прямые ссылки на объекты</h3>

Небезопасные прямые ссылки на объекты (IDOR) — это подкатегория уязвимостей управления доступом. IDOR возникает, когда приложение использует пользовательский ввод для прямого доступа к объектам, и злоумышленник может изменить ввод для получения несанкционированного доступа.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Insecure_direct_object_references/assets/1.png)

Перейдем на страницу Live chat.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Insecure_direct_object_references/assets/2.png)

Нажмем на кнопку "View transcript", чтобы просмотреть сообщение, которые уже были отправлены до этого момента. Перехватим запрос и изменим значение текстового сообщения на первое.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Insecure_direct_object_references/assets/3.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Insecure_direct_object_references/assets/4.png)

Теперь мы можем скачать файл со всеми сообщениями.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Insecure_direct_object_references/assets/5.png)

Качаем файл и открываем его. В нем мы можем найти пароль от аккаунта.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Insecure_direct_object_references/assets/6.png)

Логинимся с именем пользователя carlos и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Insecure_direct_object_references/assets/7.png)
