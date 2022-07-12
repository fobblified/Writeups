# Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped

## Theory

<h3>Выход из строки JavaScript</h3>

В тех случаях, когда контекст XSS находится внутри строкового литерала в кавычках, часто можно выйти за пределы строки и выполнить JavaScript напрямую. Крайне важно восстановить скрипт в соответствии с контекстом XSS, потому что любые синтаксические ошибки не позволят выполнить весь скрипт.

Некоторые полезные способы выхода из строкового литерала:
```
'-alert(document.domain)-'
';alert(document.domain)//
```

Некоторые приложения пытаются предотвратить выход ввода за пределы строки JavaScript, экранируя любые одинарные кавычки с помощью обратной косой черты. Обратная косая черта перед символом сообщает синтаксическому анализатору JavaScript, что символ следует интерпретировать буквально, а не как специальный символ, такой как разделитель строки. В этой ситуации приложения часто совершают ошибку, не пытаясь экранировать сам символ обратной косой черты. Это означает, что злоумышленник может использовать свой собственный символ обратной косой черты, чтобы нейтрализовать обратную косую черту, добавленную приложением.

Например, предположим, что ввод:
```
';alert(document.domain)//
```

преобразуется в:
```
\';alert(document.domain)//
```

Теперь вы можете использовать альтернативную полезную нагрузку:
```
\';alert(document.domain)//
```

которая преобразуется в:
```
\\';alert(document.domain)//
```

Здесь первая обратная косая черта означает, что вторая обратная косая черта интерпретируется буквально, а не как специальный символ. Это означает, что теперь кавычка интерпретируется как признак конца строки, и поэтому атака завершается успешно.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_a_JavaScript_string_with_angle_brackets_and_double_quotes_HTML-encoded_and_single_quotes_escaped/assets/1.png)

Отправим запрос в строке поиска и перехватим его.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_a_JavaScript_string_with_angle_brackets_and_double_quotes_HTML-encoded_and_single_quotes_escaped/assets/2.png)

Для решения будем использовать следующий эксплоит:
```
\'-alert(1)//
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_a_JavaScript_string_with_angle_brackets_and_double_quotes_HTML-encoded_and_single_quotes_escaped/assets/3.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_into_a_JavaScript_string_with_angle_brackets_and_double_quotes_HTML-encoded_and_single_quotes_escaped/assets/4.png)
