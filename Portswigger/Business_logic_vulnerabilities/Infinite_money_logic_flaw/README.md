# Infinite money logic flaw

## Theory

<h3>Недостатки домена</h3>

Во многих случаях вы столкнетесь с логическими ошибками, специфичными для бизнес-домена или цели сайта.

Дисконтная функциональность интернет-магазинов — классическая поверхность атаки при поиске логических ошибок. Это может быть потенциальной золотой жилой для злоумышленника, поскольку в способах применения скидок возникают всевозможные основные логические недостатки.

Например, рассмотрим интернет-магазин, который предлагает 10% скидку при заказе на сумму более 1000 долларов. Это может быть уязвимо для злоупотреблений, если бизнес-логика не может проверить, был ли изменен заказ после применения скидки. В этом случае злоумышленник может просто добавлять товары в свою корзину, пока не достигнет порога в 1000 долларов, а затем удалить ненужные товары перед размещением заказа. Затем они получат скидку на свой заказ, даже если он больше не соответствует предполагаемым критериям.

Вам следует уделять особое внимание любой ситуации, когда цены или другие конфиденциальные значения корректируются на основе критериев, определяемых действиями пользователя. Попытайтесь понять, какие алгоритмы использует приложение для внесения этих корректировок и в какой момент эти корректировки выполняются. Это часто связано с манипулированием приложением, чтобы оно находилось в состоянии, когда примененные корректировки не соответствуют исходным критериям, задуманным разработчиками.

Чтобы выявить эти уязвимости, нужно хорошо подумать о том, какие цели могут быть у злоумышленника, и попытаться найти разные способы их достижения, используя предоставленный функционал. Это может потребовать определенного уровня знаний в предметной области, чтобы понять, что может быть выгодно в данном контексте. Чтобы использовать простой пример, вам нужно понимать социальные сети, чтобы понять преимущества принуждения большого количества пользователей следовать за вами.

Не зная предметной области, вы можете игнорировать опасное поведение, потому что просто не знаете о его возможных побочных эффектах. Точно так же вы можете изо всех сил пытаться соединить точки и заметить, как две функции могут сочетаться вредным образом. Для простоты примеры, используемые в этом разделе, относятся к домену, с которым все пользователи уже знакомы, а именно к интернет-магазину. Однако независимо от того, занимаетесь ли вы поиском ошибок, пентестом или даже просто разработчиком, пытающимся написать более безопасный код, в какой-то момент вы можете столкнуться с приложениями из менее знакомых областей. В этом случае вы должны прочитать как можно больше документации и, если возможно, поговорить с экспертами в данной области, чтобы узнать их мнение. Это может показаться большой работой, но чем менее ясен домен, тем больше вероятность того, что другие тестировщики пропустят множество ошибок.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/1.png)

На главной странице перейдем в ее конец. Подписываемся на новостную рассылку, введя случайные данные почты.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/2.png)

Получаем промокод.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/3.png)

Логинимся с имеющимися данными. На главной странице добавляем в корзину промокод(Gift Card).

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/4.png)

В корзине добавляем полученный промокод.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/5.png)

Оставляем заказ и получаем новый промокод.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/6.png)

Вводим его на странице нашего аккаунта.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/7.png)

Мы видим, что сумма на аккаунте увеличилась.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/8.png)

Теперь мы настроим макрос для автоматического получения и активации промокодов. Перейдем в меню Project Option -> Sessions -> Session Handling Rules.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/9.png)

Нажимаем на кнопку Add. В новой панели перейдем в меню Scope. В меню URL Scope выбираем Include all URLs.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/10.png)

В панели Details добаляем новое правило Rile Actions -> Add -> Run a macro.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/11.png)

Добавляем новый макрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/12.png)

Выбираем необходимые запросы.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/13.png)

Изменим GET запрос /cart/order-confirmation..., нажав на кнопку Configure item.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/14.png)

Добавляем выборку параметра.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/15.png)

Даем имя параметру. Выделяем промокод.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/16.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/17.png)

Переходим к следующему запросу. Теперь мы будем вставлять выделенный промокод. Конфигурируем POST запрос /gift-card.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/18.png)

В пункте gift-card выбираем Dirive from prior response и Response 4. Таким образом мы будем забирать промокод, который мы выделяем в 4 запросе.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/19.png)

Найдем GET запрос /my-account входа в аккаунт и отправим его в Intruder.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/20.png)

Убираем все специальные символы.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/21.png)

В разделе Payload выбираем пункт нулевые запросы. Выставляем значение побольше, чтобы накрутилось необходимое количество денег на аккаунте.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/22.png)

Выставляем Resource Pool равным 1.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/23.png)

Запускаем атаку. Набрав необходимое количество денег на аккаунте завершаем атаку.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/24.png)

Переходим на главную страницу и добавляем в корзину нужный продукт.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/25.png)

Оставляем заказ и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Infinite_money_logic_flaw/assets/26.png)
