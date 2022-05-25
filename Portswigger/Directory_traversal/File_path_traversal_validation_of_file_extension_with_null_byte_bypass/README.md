# File path traversal, validation of file extension with null byte bypass

## Theory

<h3>Общие препятствия для использования уязвимостей обхода пути к файлу</h3>

Многие приложения, помещающие пользовательский ввод в пути к файлам, реализуют некоторую защиту от атак с обходом пути, и их часто можно обойти.

Если приложение удаляет или блокирует последовательности обхода каталога из имени файла, предоставленного пользователем, то можно обойти защиту, используя различные методы.

Если приложение требует, чтобы введенное пользователем имя файла заканчивалось ожидаемым расширением файла, например .png, то можно использовать нулевой байт для эффективного завершения пути к файлу перед требуемым расширением. Например:
```
filename=../../../etc/passwd%00.png
```

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_validation_of_file_extension_with_null_byte_bypass/assets/1.png)

Обновим страницу и перехватим запрос, приложение пытается загрузить какую-то картинку.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_validation_of_file_extension_with_null_byte_bypass/assets/2.png)

Вместо картинки мы будет отправлять запрос к файлу /etc/passwd. Приложению проверяет формат файла, поэтому, чтобы обойти защиту, мы будет отправлять запрос с требуемым форматом файла, используя нулевой байт.
```
/image/filename?=../../../etc/passwd%00.jpg
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_validation_of_file_extension_with_null_byte_bypass/assets/3.png)

Отправляем запрос и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_validation_of_file_extension_with_null_byte_bypass/assets/4.png)
