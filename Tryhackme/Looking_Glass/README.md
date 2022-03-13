# Looking Glass

:white_check_mark:  [**Vigenere Decode**](#vigenere)

:white_check_mark: [**Reboot**](#reboot)

:white_check_mark: [**Sudo**](#sudo)
___

## Enumeration
Сканируем ip командой:
```
nmap -sC -sV -p- glass.thm
```

```
PORT      STATE SERVICE    VERSION
22/tcp    open  ssh        OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 3f:15:19:70:35:fd:dd:0d:07:a0:50:a3:7d:fa:10:a0 (RSA)
|   256 a8:67:5c:52:77:02:41:d7:90:e7:ed:32:d2:01:d9:65 (ECDSA)
|_  256 26:92:59:2d:5e:25:90:89:09:f5:e5:e0:33:81:77:6a (ED25519)
9000/tcp  open  ssh        Dropbear sshd (protocol 2.0)
| ssh-hostkey:
|_  2048 ff:f4:db:79:a9:bc:b8:8a:d4:3f:56:c2:cf:cb:7d:11 (RSA)
9001/tcp  open  ssh        Dropbear sshd (protocol 2.0)
| ssh-hostkey:
|_  2048 ff:f4:db:79:a9:bc:b8:8a:d4:3f:56:c2:cf:cb:7d:11 (RSA)
9002/tcp  open  ssh        Dropbear sshd (protocol 2.0)
| ssh-hostkey:
|_  2048 ff:f4:db:79:a9:bc:b8:8a:d4:3f:56:c2:cf:cb:7d:11 (RSA)
9003/tcp  open  ssh        Dropbear sshd (protocol 2.0)
| ssh-hostkey:
|_  2048 ff:f4:db:79:a9:bc:b8:8a:d4:3f:56:c2:cf:cb:7d:11 (RSA)                                                        
```

Попробуем подключиться к одному из портов **Dropbear**. При поключении к портам получаем сообщения: "Lower", "Higher". 

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Looking_Glass/1.png)

Ищем нужный порт ssh. Найдя нужный порт, появляется сообщение с просьбой ввести пароль.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Looking_Glass/2.png)

<a name="vigenere"></a>

Попробуем расшифровать текст шифром Виженера. Воспользуемся [тулзой](https://www.boxentriq.com/code-breaking/vigenere-cipher).

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Looking_Glass/3.png)

Получаем пароль.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Looking_Glass/4.png)

Пробуем ввести его, но он не подходит. Попробуем расшифровать текст с этим ключом.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Looking_Glass/5.png)

Получаем секретную фразу.

Пробуем подключиться по ssh и вводим фразу. Получаем имя пользователя и пароль.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Looking_Glass/6.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Looking_Glass/7.png)

## Users Enumeration

<a name="reboot"></a>

Посмотрим какие команды можно выполнять от **sudo**

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Looking_Glass/8.png)

Файл "**twasBrilling.sh**" имеет нужные права. Добавим обратную оболочку в файл. Воспользуемся [ресурсом](https://www.revshells.com/) для генерации обратной оболочки. 

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Looking_Glass/9.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Looking_Glass/10.png)

Перезагружаем машину: 
```
sudo /sbin/reboot
```

На слушателе получаем обратную оболочку.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Looking_Glass/11.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Looking_Glass/12.png)

Открываем файл "**humptydumpty**" и видим хэши паролей.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Looking_Glass/13.png)

Идем на [crackstation](https://crackstation.net/) и расшифровываем хэши.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Looking_Glass/14.png)

Последний хэш расшифровать не удалось. Идем расшифровывать его на [CyberChef](https://gchq.github.io/CyberChef/).

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Looking_Glass/15.png)

Получаем пароль от юзера humptydumpty.

Пробуем использовать команду "**sudo -l**", но у нас нет прав sudo. Просмотрим права других пользователей. Видим странные права у юзера alice. Посмотреть файлы в директории не получается, но мы можем читать файлы.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Looking_Glass/16.png)

Выведем ключ RSA.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Looking_Glass/17.png)

Копируем ключ и подключаемся по ssh к юзеру alice.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Looking_Glass/18.png)

## Privilege escalation

<a name="sudo"></a>

У нас есть доступ к просмотру "**sudoers.d**". Юзер alice имеет права sudo для **/bin/bash**.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Looking_Glass/19.png)

Видим что у юзера alice есть доступ к хосту ssalg-gnikool. Используем команду **sudo**, обращаясь к хосту и используя **bash**.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Looking_Glass/20.png)

Получаем root.
