# Harder

:white_check_mark:  [**Git / git-dumper**](#git)

:white_check_mark: [**Hmac.php**](#hmac)

:white_check_mark: [**Hmac hash**](#hash)

:white_check_mark: [**Gpg import**](#gpg)
___

## Enumeration
Сканируем ip командой:
```
nmap -sC -sV harder.thm
```

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.3 (protocol 2.0)
| ssh-hostkey: 
|   4096 cf:e2:d9:27:d2:d9:f3:f7:8e:5d:d2:f9:9d:a4:fb:66 (RSA)
|_  256 1e:45:7b:0a:b5:aa:87:e6:1b:b1:b7:9f:5d:8f:85:70 (ED25519)
80/tcp open  http    nginx 1.18.0
|_http-title: Error
|_http-server-header: nginx/1.18.0                                                        
```

Перехватываем запрос с главной страницы **harder.thm** и видим название поддомена **pwd.harder.local**. Добавляем поддомен в **/etc/hosts**.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Harder/1.png)

Теперь у нас есть доступ к панели авторизации.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Harder/2_1.png)

Проходим аутентефикацию со стандартными логином и паролем **admin:admin**.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Harder/2_2.png)

Проверяем директории **pwd.harder.local**.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Harder/2.png)

```
gobuster dir -u http://pwd.harder.local -w /usr/share/wordlists/dirb/common.txt
```

```
===============================================================
[+] Url:                     http://pwd.harder.local
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/02/28 19:50:02 Starting gobuster in directory enumeration mode
===============================================================
/.git/HEAD            (Status: 200) [Size: 23]
/index.php            (Status: 200) [Size: 19926]
```

<a name="git"></a>

Находим директорию **/.git/HEAD**/. Попробуем сдампить **git** с [git-dumper](https://github.com/arthaud/git-dumper).

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Harder/3.png)

Используем команду **git checkout**. Git checkout используется для обновления заголовка с целью установки одной указанной ветви в качестве текущей ветви.
Получаем доступ к файлам:

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Harder/4.png)

<a name="hmac"></a>

Откроем файл **hmac.php**. 

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Harder/5.png)

Скрипт сначала проверяет наличие **GET[‘h’]** или **GET[‘host’]**. Если один из них пуст - соединение обрывается. Затем скрипт включает файл secret.php, которого у нас нет в репозитории git. Далее, если **GET[‘n’]** не пуст, генерируется хеш-значение SHA256 с использованием метода HMAC, и ключ достается из файла secret.php, а новый хэш сохраняется в переменной $secret. Теперь, используя этот хэш в $secret в качестве ключа, генерируется хеш SHA256 параметра **GET[‘host’]** и сохраняется в переменной $hm. И, наконец, если $hm не равно **GET['h']**, соединение обрывается. Итак, теперь мы должны предсказать окончательный результат и передать его в **GET['h']**. Чтобы обойти это правило, передаем значение **GET[’n’]** как массив, который, дает значение $secret как ложное.

<a name="hash"></a>

Создаем хэш с нужными условиями: 

```
php -r "echo hash_hmac('sha256', "pwd.harder.local", false)"

hash = 5b622e20b29bdbcb0a4881f1d117d20a33a1f78a3c07ba85645567607e75cedf
```

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Harder/6.png)

Url для эксплуатации уязвимости: 

```
http://pwd.harder.local/?n[]=&h=5b622e20b29bdbcb0a4881f1d117d20a33a1f78a3c07ba85645567607e75cedf&host=pwd.harder.local
```
![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Harder/7.png)

Добавляем новый поддомен и заходим в аккаунт.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Harder/8.png)

Получение доступа возможно только с ip 10.10.10.x . Перехватываем запрос и модифицируем путем добавления заголовка **X-Forwarded-For: 10.10.10.10**, который позволит обойти проверку ip. [Расширение](https://addons.mozilla.org/ru/firefox/addon/x-forwarded-for-injector/?utm_source=addons.mozilla.org&utm_medium=referral&utm_content=search) в Firefox позволяет подставлять этот заголовок автоматически.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Harder/9.png)

Получаем доступ к "консоли". 

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Harder/10.png)

Кидаем обратную оболочку.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Harder/11.png)

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Harder/12.png)

Находим файлы к которым имеет доступ пользователь www.
```
find / -type f -user www 2>/dev/null
```

Find находит интересный файл, выведем его в консоль.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Harder/13.png)

Теперь мы имеем доступ к машине по ssh. Пробуем найти файлы сожержащие **SUID**.
```
find / -perm -u=s 2>/dev/null
```
<a name="gpg"></a>

Просмотрим содержимое скрипта. Нужен ключ gpg.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Harder/14.png)

Ключ gpg находится в **/var/backups**. Импортируем ключ.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Harder/15.png)

## Privilege escalation

Создадим файл с командой, зашифруем ключом gpg, используем скрипт.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/Harder/16.png)
