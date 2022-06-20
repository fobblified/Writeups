# Information disclosure in version control history

## Theory

<h3>История контроля версий</h3>

Практически все веб-сайты разрабатываются с использованием какой-либо системы контроля версий, такой как Git. По умолчанию проект Git хранит все данные контроля версий в папке с именем .git. Иногда веб-сайты предоставляют доступ к этому каталогу в производственной среде. В этом случае вы можете получить к нему доступ, просто перейдя в /.git.

Хотя часто нецелесообразно вручную просматривать необработанную файловую структуру и содержимое, существуют различные способы загрузки всего каталога .git. Затем вы можете открыть его, используя локальную установку Git, чтобы получить доступ к истории контроля версий веб-сайта. Сюда могут входить журналы, содержащие зафиксированные изменения и другую интересную информацию.]

Это может не дать вам доступа к полному исходному коду, но сравнение различий позволит вам прочитать небольшие фрагменты кода. Как и в любом исходном коде, вы также можете найти конфиденциальные данные, жестко запрограммированные в некоторых измененных строках.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_in_version_control_history/assets/1.png)

Просмотрим директорию /.git. В ней мы можем найти данные, которые могут оказаться полезны нам.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_in_version_control_history/assets/2.png)

Выгрузим все эти файлы на нашу машину командой:
```
wget -r https://0a5a001b03e5d1d9c02f1bf700140056.web-security-academy.net/.git/
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_in_version_control_history/assets/3.png)

Будем использовать GUI Git для поиска изменений в коммитах. Вернем изменения(Undo Last Commit).

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_in_version_control_history/assets/4.png)

Теперь мы можем просмотреть изменения, внесенные в файл admin.conf, где мы и находим пароль администратора.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_in_version_control_history/assets/5.png)

Логинимся как администратор. Теперь мы имеем доступ к админ панели.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_in_version_control_history/assets/6.png)

Переходим в админ панель и удаляем пользователя carlos.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_in_version_control_history/assets/7.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Information_disclosure_vulnerabilities/Information_disclosure_in_version_control_history/assets/8.png)
