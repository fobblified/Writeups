# Weak isolation on dual-use endpoint

## Theory

<h3>Пользователи не всегда будут вводить обязательный ввод</h3>

Еще одно из заблуждений состоит в том, что пользователи всегда будут указывать значения для обязательных полей ввода. Браузеры могут препятствовать отправке формы обычными пользователями без обязательного ввода, но, как мы знаем, злоумышленники могут подделывать параметры при передаче. Это распространяется даже на полное удаление параметров.

Это особая проблема в тех случаях, когда несколько функций реализованы в одном сценарии на стороне сервера. В этом случае наличие или отсутствие конкретного параметра может определять, какой код будет выполняться. Удаление значений параметров может позволить злоумышленнику получить доступ к путям кода, которые должны быть недоступны.

При поиске логических ошибок вы должны попытаться удалить каждый параметр по очереди и посмотреть, как это повлияет на ответ. Вы должны убедиться, что:

* Удаляйте только один параметр за раз, чтобы обеспечить доступ ко всем соответствующим путям кода.
* Попробуйте удалить имя параметра вместе со значением. Сервер обычно обрабатывает оба случая по-разному.
* Следуйте многоступенчатым процессам до завершения. Иногда изменение параметра на одном этапе влияет на другой этап рабочего процесса.

Это относится как к параметрам URL, так и к параметрам POST, но не забывайте также проверять файлы cookie. Этот простой процесс может выявить странное поведение приложения, которое может быть использовано злоумышленниками.

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Weak_isolation_on_dual-use_endpoint/assets/1.png)

Логинимся с имеющимися данными. На странице мы можем заметить поля, для изменения пароля пользователя на новый. Чтобы изменить пароль пользователя administrator попробуем удалить проверку на существующий пароль.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Weak_isolation_on_dual-use_endpoint/assets/2.png)

Введем случайные данные отправим запрос и перехватим его.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Weak_isolation_on_dual-use_endpoint/assets/3.png)

Изменим имя пользователя на administrator, а параметр current-password вырежем полностью.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Weak_isolation_on_dual-use_endpoint/assets/4.png)

Отправляем модифицированный запрос и получаем ответ.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Weak_isolation_on_dual-use_endpoint/assets/5.png)

Логинимся под пользователя administrator, используя новый пароль.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Weak_isolation_on_dual-use_endpoint/assets/6.png)

Теперь мы имеем доступ к админ панели. Переходим в нее и удаляем пользователя carlos.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Weak_isolation_on_dual-use_endpoint/assets/7.png)

Получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Business_logic_vulnerabilities/Weak_isolation_on_dual-use_endpoint/assets/8.png)
