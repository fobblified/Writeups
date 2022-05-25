# File path traversal, simple case

## Theory

<h3>Чтение произвольных файлов через обход каталога</h3>

Рассмотрим приложение для покупок, которое отображает изображения товаров для продажи. Изображения загружаются через следующий HTML код:
```
<img src="/loadImage?filename=218.png">
```

**LoadImage** принимает параметр имени файла и возвращает содержимое указанного файла. Сами файлы изображений хранятся на диске в папке /var/www/images/. Чтобы вернуть изображение, приложение добавляет запрошенное имя файла к этому базовому каталогу и использует API файловой системы для чтения содержимого файла. В приведенном выше случае приложение читает из следующего пути к файлу:
```
/var/www/images/218.png
```

Приложение не реализует защиту от атак с обходом каталога, поэтому злоумышленник может запросить следующий URL-адрес для извлечения произвольного файла из файловой системы сервера:
```
https://insecure-website.com/loadImage?filename=../../../etc/passwd
```

Это заставляет приложение читать файл по следующему пути:
```
/var/www/images/../../../etc/passwd
```

Последовательность **../** означает переход на один уровень вверх в структуре каталогов. Три последовательные последовательности **../** поднимаются от /var/www/images/ к корню файловой системы, поэтому файл читается по следующему пути:
```
/etc/passwd
```

В операционных системах на основе Unix это стандартный файл, содержащий сведения о пользователях, зарегистрированных на сервере.

В Windows как **../**, так и **..\\** являются допустимыми последовательностями обхода каталога, и эквивалентная атака для извлечения стандартного файла операционной системы будет выглядеть так:
```
https://insecure-website.com/loadImage?filename=..\..\..\windows\win.ini
```

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_simple_case/assets/1.png)

Обновим страницу и перехватим запрос, приложение пытается загрузить какую-то картинку.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_simple_case/assets/2.png)

Вместо картинки мы будет отправлять запрос к файлу /etc/passwd.
```
/image?filename=../../../etc/passwd
```
![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_simple_case/assets/3.png)

Отправляем запрос и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_simple_case/assets/4.png)
