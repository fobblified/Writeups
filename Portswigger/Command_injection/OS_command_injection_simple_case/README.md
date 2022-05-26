# OS command injection, simple case

## Theory

<h3>Выполнение произвольных команд</h3>

Рассмотрим приложение для покупок, которое позволяет пользователю узнать, есть ли товар в наличии в определенном магазине. Доступ к этой информации осуществляется через URL-адрес, например:
```
https://insecure-website.com/stockStatus?productID=381&storeID=29
```

Чтобы предоставить информацию о запасах, приложение должно запрашивать различные системы. По историческим причинам эта функциональность реализована путем вызова команды оболочки с идентификаторами продукта и хранилища в качестве аргументов:
```
stockreport.pl 381 29
```

Эта команда выводит состояние запасов для указанного товара, которое возвращается пользователю.

Поскольку приложение не обеспечивает защиту от внедрения команд ОС, злоумышленник может ввести следующие данные для выполнения произвольной команды:
```
& echo aiwefwlguh &
```

Если этот вход представлен в параметре productID, то команда, выполняемая приложением, будет следующей:
```
stockreport.pl & echo aiwefwlguh & 29
```

Команда echo просто вызывает отображение предоставленной строки в выводе и является способом проверки некоторых типов внедрения команд ОС. Символ & является разделителем команд оболочки, поэтому на самом деле выполняются три отдельные команды одна за другой. В результате результат, возвращаемый пользователю:
```
Error - productID was not provided
aiwefwlguh
29: command not found
```

Три строки вывода демонстрируют, что:

* Исходная команда stockreport.pl была выполнена без ожидаемых аргументов и вернула сообщение об ошибке.
* Введенная команда эха была выполнена, и предоставленная строка была отражена в выводе.
* Исходный аргумент 29 был выполнен как команда, что вызвало ошибку.

Размещение дополнительного разделителя команд & после введенной команды обычно полезно, поскольку он отделяет введенную команду от всего, что следует за точкой внедрения. Это снижает вероятность того, что последующие действия предотвратят выполнение введенной команды.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/OS_command_injection_simple_case/assets/1.png)

Перейдем к одному из продуктов на странице.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/OS_command_injection_simple_case/assets/2.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/OS_command_injection_simple_case/assets/3.png)

Нажмем на кнопку "Check stock" и перехватим запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/OS_command_injection_simple_case/assets/4.png)

Модифицируем запрос:
```
productId=2&storeId=3|whoami
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/OS_command_injection_simple_case/assets/5.png)

Отправляем запрос и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Command_injection/OS_command_injection_simple_case/assets/6.png)
