# DOM XSS in innerHTML sink using source location.search

## Theory

<h3>Использование DOM XSS с различными источниками и приемниками</h3>

В принципе, веб-сайт уязвим для межсайтовых сценариев на основе DOM, если существует исполняемый путь, по которому данные могут распространяться от источника к приемнику. На практике разные источники и приемники имеют разные свойства и поведение, которые могут повлиять на возможность использования и определить, какие методы необходимы. Кроме того, сценарии веб-сайта могут выполнять проверку или другую обработку данных, которые необходимо учитывать при попытке использовать уязвимость. Существует множество приемников, которые относятся к уязвимостям на основе DOM. 

Приемник innerHTML не принимает элементы скрипта ни в одном современном браузере, и события svg onload не срабатывают. Это означает, что вам нужно будет использовать альтернативные элементы, такие как img или iframe. Обработчики событий, такие как onload и onerror, можно использовать вместе с этими элементами. Например:
```
element.innerHTML='... <img src=1 onerror=alert(document.domain)> ...'
```

## Writeup

Главная страница:

![](./assets/1.png)

В строке поиска отправим следующий эксплоит:
```
<img src=1 onerror=alert(1)>
```

![](./assets/2.png)

Мы успешно вызвали alert, и так теперь выглядит наша страница, что подтверждает, что эксплоит сработал.

![](./assets/3.png)

Получаем ответ от сервера.

![](./assets/4.png)