# Daily Bugle

:white_check_mark:  [**Joomla 3.7.0 / Joomblah**](#joomla)

:white_check_mark: [**Yum(privesc)**](#yum)

___

## Enumeration
Сканируем ip командой:
```
nmap -sC -sV bugle.thm
```

```
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 68:ed:7b:19:7f:ed:14:e6:18:98:6d:c5:88:30:aa:e9 (RSA)
|   256 5c:d6:82:da:b2:19:e3:37:99:fb:96:82:08:70:ee:9d (ECDSA)
|_  256 d2:a9:75:cf:2f:1e:f5:44:4f:0b:13:c2:0f:d7:37:cc (ED25519)
80/tcp   open  http    Apache httpd 2.4.6 ((CentOS) PHP/5.6.40)
|_http-generator: Joomla! - Open Source Content Management
|_http-title: Home
| http-robots.txt: 15 disallowed entries 
| /joomla/administrator/ /administrator/ /bin/ /cache/ 
| /cli/ /components/ /includes/ /installation/ /language/ 
|_/layouts/ /libraries/ /logs/ /modules/ /plugins/ /tmp/
|_http-server-header: Apache/2.4.6 (CentOS) PHP/5.6.40
3306/tcp open  mysql   MariaDB (unauthorized)
```

Главная страница **bugle.thm**:

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Daily_Bugle/1.png)

Сканируем директории командой:
```
gobuster dir -u http://bugle.thm -t 100 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .php,.txt,.html
```

```
/images               (Status: 301) [Size: 232] [--> http://bugle.thm/images/]
/media                (Status: 301) [Size: 231] [--> http://bugle.thm/media/] 
/templates            (Status: 301) [Size: 235] [--> http://bugle.thm/templates/]
/index.php            (Status: 200) [Size: 9268]                                 
/modules              (Status: 301) [Size: 233] [--> http://bugle.thm/modules/]  
/bin                  (Status: 301) [Size: 229] [--> http://bugle.thm/bin/]      
/plugins              (Status: 301) [Size: 233] [--> http://bugle.thm/plugins/]  
/includes             (Status: 301) [Size: 234] [--> http://bugle.thm/includes/] 
/language             (Status: 301) [Size: 234] [--> http://bugle.thm/language/] 
/README.txt           (Status: 200) [Size: 4494]                                 
/components           (Status: 301) [Size: 236] [--> http://bugle.thm/components/]
/cache                (Status: 301) [Size: 231] [--> http://bugle.thm/cache/]     
/libraries            (Status: 301) [Size: 235] [--> http://bugle.thm/libraries/] 
/robots.txt           (Status: 200) [Size: 836]                                   
/tmp                  (Status: 301) [Size: 229] [--> http://bugle.thm/tmp/]       
/LICENSE.txt          (Status: 200) [Size: 18092]                                 
/layouts              (Status: 301) [Size: 233] [--> http://bugle.thm/layouts/]   
/administrator        (Status: 301) [Size: 239] [--> http://bugle.thm/administrator/]
/configuration.php    (Status: 200) [Size: 0]                                        
/htaccess.txt         (Status: 200) [Size: 3005]                                     
/cli                  (Status: 301) [Size: 229] [--> http://bugle.thm/cli/]
```

<a name="joomla"></a>

## Admin Panel
Посмотрим файл README.txt. В нем мы можем найти версию joomla, а именно 3.7.0. Найдем под нее [эксплоит](https://github.com/stefanlucas/Exploit-Joomla).

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Daily_Bugle/2.png)

## Hash
Получаем имя пользователя и хэш пароля. При помощи john сбрутим пароль.
```
john hash --wordlist=/usr/share/wordlists/rockyou.txt > hash_cracked
```

## Getting shell
Логинимся в joomla (bugle.thm/administrator).

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Daily_Bugle/3.png)

Получаем доступ к панели. Загрузим shell. 

1. **Extensions -> Templates -> Templates**
2. **Beez3 Details and Files**
3. **Создаем новый файл**
4. **Вставляем reverse shell**
5. **Сохраняем**
6. **Запускаем слушатель**
7. **Открываем shell: bugle.thm/templates/beez3/shell.php**

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Daily_Bugle/4.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Daily_Bugle/5.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Daily_Bugle/6.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Daily_Bugle/7.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Daily_Bugle/8.png)

<a name="yum"></a>

## Getting root

Просмотри права пользователя командой: **sudo -l**.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Daily_Bugle/9.png)

На [GTFOBins](https://gtfobins.github.io/gtfobins/yum/) найдем эксплоит.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Daily_Bugle/10.png)

Прописываем все команды и получаем рута.
