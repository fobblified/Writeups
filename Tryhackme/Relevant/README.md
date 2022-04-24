# Relevant

:white_check_mark:  [**Samba directory browsing**](#samba)

:white_check_mark: [**SeImpersonatePrivilege exploit(privesc)**](#SeImpersonate)
___

## Enumeration
Сканируем ip командой:
```
nmap -sC -sV -p- relevant.thm
```

```
PORT      STATE SERVICE       VERSION                                                                                                                     
80/tcp    open  http          Microsoft IIS httpd 10.0                                                                                                    
|_http-server-header: Microsoft-IIS/10.0                                                                                                                  
|_http-title: IIS Windows Server                                                                                                                             
| http-methods:                                                                                                                                              
|_  Potentially risky methods: TRACE                                                                                                                      
135/tcp   open  msrpc         Microsoft Windows RPC                                                                                                       
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn                                                                                               
445/tcp   open  microsoft-ds  Windows Server 2016 Standard Evaluation 14393 microsoft-ds
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2022-02-23T14:44:13+00:00; +2s from scanner time.
| ssl-cert: Subject: commonName=Relevant
| Not valid before: 2022-02-22T14:23:03
|_Not valid after:  2022-08-24T14:23:03
| rdp-ntlm-info: 
|   Target_Name: RELEVANT
|   NetBIOS_Domain_Name: RELEVANT
|   NetBIOS_Computer_Name: RELEVANT
|   DNS_Domain_Name: Relevant
|   DNS_Computer_Name: Relevant
|   Product_Version: 10.0.14393
|_  System_Time: 2022-02-23T14:43:33+00:00
49663/tcp open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
49667/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows
```

Главная страница **relevant.thm**:

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Relevant/1.png)

<a name="samba"></a>

Посмотри что находится на **samba**:
```
smbclient -L relevant.thm
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Relevant/2.png)

Находим директорию **nt4wrksv**, посмотрим, что в ней.
```
smbclient \\\\relevant.thm\\nt4wrksv
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Relevant/3.png)

Находим файл с паролями, скачаем его:
```
get passwords.txt
```

Декодируем и получаем данные пользователей, которые увы, никуда нельзя подставить, т.к. это rabbit hole.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Relevant/4.png)

Просканировав url через **gobuster** находим директорию **nt4wrksv**. В эту директорию можно загрузить свой **shell** через **samba**. Покопавшись в интернете находим [shell](https://github.com/borjmz/aspx-reverse-shell/blob/master/shell.aspx).

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Relevant/5.png)

Запускаем слушатель и открываем shell через url.
```
http://relevant.thm/nt4wrksv/shell.aspx
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Relevant/6.png)

<a name="SeImpersonate"></a>

Подключившись к машине, посмотрим права командой:
```
whoami /priv
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Relevant/7.png)

Мы можем видеть SeImpersonate privileges, которые могут быть использованы для повышения привилегий. Находим [эксплоит](https://github.com/dievus/printspoofer)

Закидываем эксплоит на **samba**, он будет находиться в директории **C:\inetpub\wwwroot\nt4wrksv**.

Запускаем эксплоит командой:
```
PrintSpoofer -i -c cmd
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Relevant/6.png)

Получаем nt authority\system
