# Reflected XSS with AngularJS sandbox escape without strings

## Theory

<h3>Что такое песочница AngularJS?</h3>

Песочница AngularJS — это механизм, который предотвращает доступ к потенциально опасным объектам, таким как окно или документ, в выражениях шаблона AngularJS. Это также предотвращает доступ к потенциально опасным свойствам, таким как __proto__. Несмотря на то, что команда AngularJS не считает его границей безопасности, более широкое сообщество разработчиков обычно думает иначе. Хотя обход «песочницы» изначально был сложной задачей, исследователи безопасности обнаружили множество способов сделать это. В результате он был удален из AngularJS в версии 1.6. Однако многие устаревшие приложения по-прежнему используют более старые версии AngularJS и в результате могут быть уязвимы.

<h3>Как работает песочница AngularJS?</h3>

Песочница работает, анализируя выражение, переписывая JavaScript, а затем используя различные функции для проверки того, содержит ли переписанный код какие-либо опасные объекты. Например, функция sureSafeObject() проверяет, ссылается ли данный объект сам на себя. Например, это один из способов обнаружения объекта окна. Конструктор Function обнаруживается примерно таким же образом, проверяя, ссылается ли свойство конструктора на себя.

Функция sureSafeMemberName() проверяет доступ к каждому свойству объекта и, если он содержит опасные свойства, такие как __proto__ или __lookupGetter__, объект будет заблокирован. Функция sureSafeFunction() предотвращает вызовы call(), apply(), bind() или constructor().

<h3>Как работает побег из песочницы AngularJS?</h3>

Побег из песочницы заключается в том, чтобы обмануть песочницу, заставив ее думать, что вредоносное выражение безобидно. Самый известный escape использует модифицированную функцию charAt() глобально внутри выражения:
```
'a'.constructor.prototype.charAt=[].join
```

Когда это было первоначально обнаружено, AngularJS не предотвратил эту модификацию. Атака работает путем перезаписи функции с помощью метода [].join, в результате чего функция charAt() возвращает все отправленные ей символы, а не конкретный одиночный символ. Из-за логики функции isIdent() в AngularJS она сравнивает то, что считает одним символом, с несколькими символами. Поскольку одиночных символов всегда меньше, чем нескольких, функция isIdent() всегда возвращает значение true, как показано в следующем примере:
```
isIdent= function(ch) {
return ('a' <= ch && ch <= 'z' ||
'A' <= ch && ch <= 'Z' ||
'_' === ch || ch === '$');
}
isIdent('x9=9a9l9e9r9t9(919)')
```

Как только функция isIdent() будет обманута, вы сможете внедрить вредоносный код JavaScript. Например, такое выражение, как $eval('x=alert(1)') будет разрешено, потому что AngularJS рассматривает каждый символ как идентификатор. Обратите внимание, что нам нужно использовать функцию AngularJS $eval(), потому что перезапись функции charAt() вступит в силу только после выполнения изолированного кода. Затем этот метод обходит песочницу и разрешает произвольное выполнение JavaScript.

<h3>Создание расширенного побега из песочницы AngularJS</h3>

Итак, вы узнали, как работает базовый побег из песочницы, но вы можете столкнуться с сайтами, которые более ограничивают допустимые символы. Например, сайт может запретить вам использовать двойные или одинарные кавычки. В этой ситуации вам нужно использовать такие функции, как String.fromCharCode(), для генерации ваших символов. Хотя AngularJS запрещает доступ к конструктору String в выражении, вы можете обойти это, используя вместо этого свойство конструктора строки. Очевидно, что для этого требуется строка, поэтому для создания такой атаки вам нужно будет найти способ создания строки без использования одинарных или двойных кавычек.

В стандартном побеге из песочницы вы должны использовать $eval() для выполнения полезной нагрузки JavaScript, но в приведенной ниже лабораторной работе функция $eval() не определена. К счастью, вместо этого мы можем использовать фильтр orderBy. Типичный синтаксис фильтра orderBy выглядит следующим образом:
```
[123]|orderBy:'Some string'
```

Обратите внимание, что | оператор имеет другое значение, чем в JavaScript. Обычно это операция побитового ИЛИ, но в AngularJS она указывает на операцию фильтрации. В приведенном выше коде мы отправляем массив [123] слева в фильтр orderBy справа. Двоеточие означает аргумент для отправки фильтру, который в данном случае является строкой. Фильтр orderBy обычно используется для сортировки объекта, но он также принимает выражение, что означает, что мы можем использовать его для передачи полезной нагрузки.

## Writeup

Главная страница:

![](./assets/1.png)

Для решения будем использовать следующий экплоит:
```
https://0a8f004603320401c05b686f004100b3.web-security-academy.net/?%27accesskey=%27x%27onclick=%27alert(1)https://0aad007804b6eeeac0620145004900fe.web-security-academy.net/?search=1&toString().constructor.prototype.charAt%3d[].join;[1]|orderBy:toString().constructor.fromCharCode(120,61,97,108,101,114,116,40,49,41)=1
```

**toString()** - для создания строки без использования кавычек.

**orderBy** - массив передается фильтру orderBy

Затем мы устанавливаем аргумент для фильтра, используя **toString()** для создания строки и свойства конструктора **String**.

Используем метод **fromCharCode** для создания полезной нагрузки путем преобразования кодов символов в строку **x=alert(1)**.

Получаем ответ от сервера.

![](./assets/2.png)
