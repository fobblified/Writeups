# File path traversal, traversal sequences blocked with absolute path bypass

## Theory

<h3>Общие препятствия для использования уязвимостей обхода пути к файлу</h3>

Многие приложения, помещающие пользовательский ввод в пути к файлам, реализуют некоторую защиту от атак с обходом пути, и их часто можно обойти.

Если приложение удаляет или блокирует последовательности обхода каталога из имени файла, предоставленного пользователем, то можно обойти защиту, используя различные методы.

Возможно, вы сможете использовать абсолютный путь от корня файловой системы, такой как filename=/etc/passwd для прямой ссылки на файл без использования каких-либо последовательностей обхода.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_traversal_sequences_blocked_with_absolute_path_bypass/assets/1.png)

Обновим страницу и перехватим запрос, приложение пытается загрузить какую-то картинку.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_traversal_sequences_blocked_with_absolute_path_bypass/assets/2.png)

Вместо картинки мы будет отправлять запрос к файлу /etc/passwd.
```
/image/filename?=/etc/passwd
```
![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_traversal_sequences_blocked_with_absolute_path_bypass/assets/3.png)

Отправляем запрос и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_traversal_sequences_blocked_with_absolute_path_bypass/assets/4.png)
