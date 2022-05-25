# File path traversal, validation of start of path

## Theory

<h3>Общие препятствия для использования уязвимостей обхода пути к файлу</h3>

Многие приложения, помещающие пользовательский ввод в пути к файлам, реализуют некоторую защиту от атак с обходом пути, и их часто можно обойти.

Если приложение удаляет или блокирует последовательности обхода каталога из имени файла, предоставленного пользователем, то можно обойти защиту, используя различные методы.

Если приложение требует, чтобы введенное пользователем имя файла начиналось с ожидаемой базовой папки, такой как /var/www/images, то можно включить требуемую базовую папку, за которой следуют подходящие последовательности обхода. Например:
```
filename=/var/www/images/../../../etc/passwd
```

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_validation_of_start_of_path/assets/1.png)

Обновим страницу и перехватим запрос, приложение пытается загрузить какую-то картинку.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_validation_of_start_of_path/assets/2.png)

Вместо картинки мы будет отправлять запрос к файлу /etc/passwd. Чтобы обойти защиту, мы будет отправлять запрос с требуемыми приложением директориями.
```
/image/filename?=/var/www/images/../../../etc/passwd
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_validation_of_start_of_path/assets/3.png)

Отправляем запрос и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_validation_of_start_of_path/assets/4.png)
