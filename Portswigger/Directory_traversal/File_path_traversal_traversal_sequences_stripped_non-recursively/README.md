# File path traversal, traversal sequences stripped non-recursively

## Theory

<h3>Общие препятствия для использования уязвимостей обхода пути к файлу</h3>

Многие приложения, помещающие пользовательский ввод в пути к файлам, реализуют некоторую защиту от атак с обходом пути, и их часто можно обойти.

Если приложение удаляет или блокирует последовательности обхода каталога из имени файла, предоставленного пользователем, то можно обойти защиту, используя различные методы.

Возможно, вы сможете использовать вложенные последовательности обхода, такие как ....// или ....\\/, которые вернутся к простым последовательностям обхода при удалении внутренней последовательности.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_traversal_sequences_stripped_non-recursively/assets/1.png)

Обновим страницу и перехватим запрос, приложение пытается загрузить какую-то картинку.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_traversal_sequences_stripped_non-recursively/assets/2.png)

Вместо картинки мы будет отправлять запрос к файлу /etc/passwd. Чтобы обойти защиту, удвоим символы, которые затем будут стерты при проверке приложением.
```
/image/filename?=....//....//....//etc//passwd
```
![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_traversal_sequences_stripped_non-recursively/assets/3.png)

Отправляем запрос и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_traversal_sequences_stripped_non-recursively/assets/4.png)
