# Inconsistent handling of exceptional input

## Theory

<h3>Неспособность обрабатывать нетрадиционный ввод</h3>

Одной из целей логики приложения является ограничение пользовательского ввода значениями, которые соответствуют бизнесс-правилам. Например, приложение может принимать произвольные значения определенного типа данных, но логика определяет, является ли это значение приемлемым с точки зрения бизнеса. Многие приложения включают числовые ограничения в свою логику. Сюда могут входить ограничения, предназначенные для управления запасами, применения бюджетных ограничений, инициирования этапов цепочки поставок и т. д.

Возьмем простой пример интернет-магазина. При заказе продуктов пользователи обычно указывают количество, которое они хотят заказать. Хотя любое целое число теоретически допустимо, бизнес-логика например может запретить пользователям заказывать больше единиц товара, чем имеется в наличии.

Чтобы реализовать такие правила, разработчикам необходимо предусмотреть все возможные сценарии и включить способы их обработки в логику приложения. Другими словами, они должны сообщить приложению, должно ли оно разрешать данный ввод и как оно должно реагировать в зависимости от различных условий. Если нет явной логики для обработки данного случая, это может привести к неожиданному и потенциально опасному поведению.

Например, числовой тип данных может принимать отрицательные значения. В зависимости от связанной функциональности бизнес-логика может не допускать этого. Однако, если приложение не выполняет адекватную проверку на стороне сервера и не отклоняет этот ввод, злоумышленник может передать отрицательное значение и вызвать нежелательное поведение.

Рассмотрим перевод средств между двумя банковскими счетами. Эта функция почти наверняка проверит, достаточно ли средств у отправителя, прежде чем завершить перевод.
```
$transferAmount = $_POST['amount'];
$currentBalance = $user->getBalance();

if ($transferAmount <= $currentBalance) {
    // Complete the transfer
} else {
    // Block the transfer: insufficient funds
}
```

Но если логика недостаточно препятствует тому, чтобы пользователи указывали отрицательное значение в параметре суммы, злоумышленник может использовать это как для обхода проверки баланса, так и для перевода средств в «неправильном» направлении. Если злоумышленник отправил -1000 долларов на счет жертвы, вместо этого он может получить 1000 долларов от жертвы. Логика всегда будет оценивать, что -1000 меньше текущего баланса, и одобрять перевод.

Простые логические ошибки, подобные этой, могут иметь разрушительные последствия, если они возникают в правильной функциональности. Их также легко пропустить как во время разработки, так и во время тестирования, особенно с учетом того, что такие входные данные могут быть заблокированы элементами управления на стороне клиента в веб-интерфейсе.

При аудите приложения вы должны использовать такие инструменты, как Burp Proxy и Repeater, чтобы попытаться отправить нестандартные значения. В частности, попробуйте ввести диапазоны, которые законные пользователи вряд ли когда-либо введут. Это включает в себя исключительно высокие или исключительно низкие числовые входные данные и ненормально длинные строки для текстовых полей. Вы даже можете попробовать неожиданные типы данных. Наблюдая за реакцией приложения, вы должны попытаться ответить на следующие вопросы:

* Существуют ли какие-либо ограничения, которые накладываются на данные?
* Что происходит, когда мы достигаем этих пределов?
* Выполняется ли какое-либо преобразование или нормализация вашего ввода?

Это может привести к слабой проверке ввода, что позволит вам манипулировать приложением необычным образом. Имейте в виду, что если вы найдете одну форму на целевом веб-сайте, которая не может безопасно обрабатывать нетрадиционный ввод, вполне вероятно, что другие формы будут иметь те же проблемы.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_handling_of_exceptional_input/assets/1.png)

Перейдем в меню Site map. Выбираем наш сайт, открываем выпадаеющее меню. Находим функцию Engagement tools -> Discover content.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_handling_of_exceptional_input/assets/2.png)

Запускаем сканирование.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_handling_of_exceptional_input/assets/3.png)

Находим страницу /admin.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_handling_of_exceptional_input/assets/4.png)

Перейдем к этой странице. На ней можно увидеть, что только пользователь DontWannaCry имеет доступ к админ панеле.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_handling_of_exceptional_input/assets/5.png)

Попробуем создать нового пользователя. Перейдем на наш Email client и скопируем адрес.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_handling_of_exceptional_input/assets/6.png)

Перейдем на страницу решистрации. Введем случайные имя пользователя и пароль. В строке email введем очень длинную строку и допишем к ней наш адрес. 

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_handling_of_exceptional_input/assets/7.png)

Регистрируем пользователя и потверждаем регистрацию на странице Email client.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_handling_of_exceptional_input/assets/8.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_handling_of_exceptional_input/assets/9.png)

Логинимся под нового пользователя. Можно заметить, что нам отправляется адрес электронной почты, который мы указали.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_handling_of_exceptional_input/assets/10.png)

Но если мы превысим то, количество символов, которое зарезервировано под адрес электронной почты, то мы можем создать нового пользователя с адресом почты админа.

Снова скопируем наш адрес. Вставляем его в адресную строку вместе с адресом админа. Регистрируя нового пользователя, пытаемся определить, количество символов необходимых, чтобы обрезалась часть с нашим адресом и чтобы остался только адрес админа.
```
<long-string>@dontwannacry.com.exploit-ac811f631e8f76d4c0c2366e010500c7.web-security-academy.net
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_handling_of_exceptional_input/assets/11.png)

Подтвердаем запрос на странице Email client.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_handling_of_exceptional_input/assets/12.png)

Логинимся под новым пользователем и видим, что часть адреса, а имеено наш адрес почты обрезался и остался только адрес админа. Так же мы получили доступ к админ панеле.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_handling_of_exceptional_input/assets/13.png)

Переходим в админ панель и удаляем пользователя carlos.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_handling_of_exceptional_input/assets/14.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Inconsistent_handling_of_exceptional_input/assets/15.png)
