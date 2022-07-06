# Basic SSRF against the local server

## Theory

<h3>SSRF-атаки на сам сервер</h3>

При атаке SSRF на сам сервер злоумышленник побуждает приложение сделать HTTP-запрос обратно к серверу, на котором размещено приложение, через сетевой интерфейс обратной связи. Обычно это включает предоставление URL-адреса с именем хоста, например 127.0.0.1 (зарезервированный IP-адрес, указывающий на адаптер обратной связи) или localhost (обычно используемое имя для того же адаптера).

Например, рассмотрим приложение для покупок, которое позволяет пользователю узнать, есть ли товар в наличии в конкретном магазине. Чтобы предоставить информацию о запасах, приложение должно запрашивать различные серверные REST API, в зависимости от рассматриваемого продукта и магазина. Эта функция реализуется путем передачи URL-адреса соответствующей внутренней конечной точке API через внешний HTTP-запрос. Поэтому, когда пользователь просматривает состояние запасов товара, его браузер делает такой запрос:
```
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://stock.weliketoshop.net:8080/product/stock/check%3FproductId%3D6%26storeId%3D1
```

Это заставляет сервер сделать запрос к указанному URL-адресу, получить статус запаса и вернуть его пользователю.

В этой ситуации злоумышленник может изменить запрос, указав URL-адрес, локальный для самого сервера. Например:
```
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://localhost/admin
```

Здесь сервер извлечет содержимое URL-адреса /admin и вернет его пользователю.

Теперь, конечно, злоумышленник может просто посетить URL-адрес /admin напрямую. Но административная функциональность обычно доступна только для подходящих аутентифицированных пользователей. Таким образом, злоумышленник, который просто посещает URL-адрес напрямую, не увидит ничего интересного. Однако, когда запрос к URL-адресу /admin поступает с самого локального компьютера, обычные средства контроля доступа игнорируются. Приложение предоставляет полный доступ к административным функциям, поскольку запрос исходит из надежного источника.

Почему приложения ведут себя таким образом и неявно доверяют запросам, поступающим с локальной машины? Это может возникнуть по разным причинам:

* Проверка контроля доступа может быть реализована в другом компоненте, расположенном перед сервером приложений. При обратном подключении к самому серверу проверка обходится.
* В целях аварийного восстановления приложение может разрешить административный доступ без входа в систему любому пользователю с локального компьютера. Это дает администратору возможность восстановить систему в случае потери учетных данных. Здесь предполагается, что только полностью доверенный пользователь будет приходить непосредственно с самого сервера.
* Административный интерфейс может прослушивать номер порта, отличный от номера порта основного приложения, и поэтому пользователи могут быть недоступны напрямую.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Basic_SSRF_against_the_local_server/assets/1.png)

Выберем любой продукт.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Basic_SSRF_against_the_local_server/assets/2.png)

Перейдем на его страницу и спустимся в самый низ.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Basic_SSRF_against_the_local_server/assets/3.png)

Нажмем на кнопку "Check stock" и перехватим запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Basic_SSRF_against_the_local_server/assets/4.png)

Изменим stockApi на http://localhost/admin/

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Basic_SSRF_against_the_local_server/assets/5.png)

Отправлем запрос, на странице теперь мы также можем видеть страницу /admin.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Basic_SSRF_against_the_local_server/assets/6.png)

Попробовав удалить пользователя, нас встречает сообщение, о том функции администратора доступны только после аутентифкации. 

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Basic_SSRF_against_the_local_server/assets/7.png)

Перехватим запрос удаления пользователя. Убращаемся к локальному серверу. Отправляем запрос.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Basic_SSRF_against_the_local_server/assets/8.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/(SSRF)_Server-side_request_forgery/Basic_SSRF_against_the_local_server/assets/9.png)
