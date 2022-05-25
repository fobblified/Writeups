# File path traversal, traversal sequences stripped with superfluous URL-decode

## Theory

<h3>Общие препятствия для использования уязвимостей обхода пути к файлу</h3>

Многие приложения, помещающие пользовательский ввод в пути к файлам, реализуют некоторую защиту от атак с обходом пути, и их часто можно обойти.

Если приложение удаляет или блокирует последовательности обхода каталога из имени файла, предоставленного пользователем, то можно обойти защиту, используя различные методы.

В некоторых контекстах, например, в URL-пути или параметре имени файла запроса multipart/form-data, веб-серверы могут удалять любые последовательности обхода каталога перед передачей вашего ввода в приложение. Иногда вы можете обойти этот вид очистки с помощью кодировки URL или даже двойной кодировки URL, символов ../, что приводит к %2e%2e%2f или %252e%252e%252f соответственно. Различные нестандартные кодировки, такие как ..%c0%af или ..%ef%bc%8f, также могут помочь.

## Writeup

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_traversal_sequences_stripped_with_superfluous_URL-decode/assets/1.png)

Обновим страницу и перехватим запрос, приложение пытается загрузить какую-то картинку.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_traversal_sequences_stripped_with_superfluous_URL-decode/assets/2.png)

Вместо картинки мы будет отправлять запрос к файлу /etc/passwd. Чтобы обойти защиту, мы будет отправлять символ **/** в двойной URL кодировке. В запросе выделяем символ /, открываем меню **Convert selection -> URL -> URL-encode all characters**.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_traversal_sequences_stripped_with_superfluous_URL-decode/assets/3.png)

Запрос будет выглядеть следующим образом.
```
/image/filename?=..%252f..%252f..%252fetc%252fpasswd
```
![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_traversal_sequences_stripped_with_superfluous_URL-decode/assets/4.png)

Отправляем запрос и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/Directory_traversal/File_path_traversal_traversal_sequences_stripped_with_superfluous_URL-decode/assets/5.png)
