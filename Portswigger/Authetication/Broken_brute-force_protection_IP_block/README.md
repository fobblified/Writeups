# Broken brute-force protection, IP block

## Theory

<h3>Несовершенная защита от брутфорса</h3>

При попытке брутфорса нужно обратить особое внимание на любые различия в:

Весьма вероятно, что брутфорс будет включать в себя множество неудачных догадок, прежде чем злоумышленник успешно скомпрометирует учетную запись. Логически, защита от брутфорса вращается вокруг попытки максимально усложнить автоматизацию процесса и снизить скорость, с которой злоумышленник может пытаться войти в систему. Двумя наиболее распространенными способами предотвращения атак грубой силы являются:

* Блокировка учетной записи, к которой пытается получить доступ удаленный пользователь, если он делает слишком много неудачных попыток входа в систему.
* Блокировка IP-адреса удаленного пользователя, если он делает слишком много попыток входа в систему в быстрой последовательности.

Оба подхода предлагают разную степень защиты, но ни один из них не является неуязвимым, особенно если он реализован с использованием ошибочной логики.

Например, иногда вы можете обнаружить, что ваш IP-адрес заблокирован, если вы не можете войти в систему слишком много раз. В некоторых реализациях счетчик количества неудачных попыток сбрасывается, если владелец IP успешно входит в систему. Это означает, что злоумышленнику просто нужно будет входить в свою учетную запись каждые несколько попыток, чтобы предотвратить достижение этого предела. В этом случае простого включения ваших собственных учетных данных через регулярные промежутки времени в список слов достаточно, чтобы сделать эту защиту практически бесполезной.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter
    * Логин атакуемого пользователя: carlos

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_IP_block/assets/1.png)

Страница аутентификации:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_IP_block/assets/2.png)

Введем случайные значения имени пользователя и пароля. В ответ мы получаем строку "Incorrect password".

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_IP_block/assets/3.png)

Отправив еще несколько таких запросов, мы получаем соощение о бане: "**You have made too many incorrect login attempts. Please try again in 1 minute(s).**".

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_IP_block/assets/4.png)

Однако, введя учетные данные нашего пользователя wiener и попробовав подобрать пароль от пользователя carlos, блокировка снимается.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_IP_block/assets/5.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_IP_block/assets/6.png)

Создадим новые словари для брутфорса. Для словаря с именами пользователей будем чередовать имена wiener и carlos. Для словаря с паролями будем чередовать пароль от нашего аккаунта и уже имеющиеся слова в словаре. 
```
for user in {1..100}; do echo wiener; echo carlos; done >> usernames_new
```

```
for pass in $(cat passwords); do echo peter; echo $pass; done
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_IP_block/assets/7.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_IP_block/assets/8.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_IP_block/assets/9.png)

Отправляем запрос в Burp Intruder, ставим перебор методом **Pitchfork**. Вставим имя пользователя и пароль в специальные символы.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_IP_block/assets/10.png)

Ограничим количество попыток ввода пароля при переборе, чтобы не возникло лишних срабатываний.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_IP_block/assets/11.png)

Запускаем перебор. Выставим фильтр по коду ответа. Мы можем увидеть, что единственный запрос, который имеет имя пользователя carlos так же имеет 302 код ответа.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_IP_block/assets/12.png)

Логинимся с уже известными нам данными от пользователя carlos. Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_IP_block/assets/13.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Authetication/Broken_brute-force_protection_IP_block/assets/14.png)
