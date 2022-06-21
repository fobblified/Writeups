# Unprotected admin functionality

## Theory

<h3>Незащищенная функциональность</h3>

В основном, вертикальное повышение привилегий возникает, когда приложение не обеспечивает никакой защиты конфиденциальной функциональности. Например, административные функции могут быть связаны со страницей приветствия администратора, но не со страницы приветствия пользователя. Однако пользователь может просто получить доступ к административным функциям, перейдя непосредственно по соответствующему URL-адресу администратора.

Например, на веб-сайте могут размещаться конфиденциальные функции по следующему URL-адресу:
```
https://insecure-website.com/admin
```

На самом деле это может быть доступно любому пользователю, а не только администраторам, у которых есть ссылка на эту функцию в их пользовательском интерфейсе. В некоторых случаях административный URL-адрес может быть раскрыт в других местах, например в файле robots.txt:
```
https://insecure-website.com/robots.txt
```

Даже если URL-адрес нигде не раскрывается, злоумышленник может использовать список слов для подбора местоположения конфиденциальных функций.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Unprotected_admin_functionality/assets/1.png)

Проверим файл robots.txt. В нем мы можем найти директорию /administrator-panel.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Unprotected_admin_functionality/assets/2.png)

Перейдем на страницу /administrator-panel.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Unprotected_admin_functionality/assets/3.png)

Удаляем пользователя carlos и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Access_control/Unprotected_admin_functionality/assets/4.png)
