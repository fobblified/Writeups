# Web shell upload via obfuscated file extension

## Theory

<h3>Скрытие расширений файлов</h3>

Даже самые исчерпывающие черные списки потенциально можно обойти с помощью классических методов запутывания. Предположим, что код проверки чувствителен к регистру и не может распознать, что эксплойт.pHp на самом деле является файлом .php. Если код, который впоследствии сопоставляет расширение файла с типом MIME, не чувствителен к регистру, это несоответствие позволяет вам скрыть вредоносные файлы PHP после проверки, которые в конечном итоге могут быть выполнены сервером.

Вы также можете добиться аналогичных результатов, используя следующие методы:

* Предоставьте несколько расширений. В зависимости от алгоритма, используемого для разбора имени файла, следующий файл может быть интерпретирован либо как файл PHP, либо как изображение JPG:exploit.php.jpg

* Добавьте конечные символы. Некоторые компоненты удаляют или игнорируют конечные пробелы, точки и т. п.:exploit.php.

* Попробуйте использовать кодировку URL (или двойную кодировку URL) для точек, косой черты и обратной косой черты. Если значение не декодируется при проверке расширения файла, но позже декодируется на стороне сервера, это также может позволить вам загружать вредоносные файлы, которые в противном случае были бы заблокированы: exploit%2Ephp

* Добавьте точку с запятой или символы нулевого байта в URL-кодировке перед расширением файла. Если проверка написана на языке высокого уровня, таком как PHP или Java, но сервер обрабатывает файл, используя низкоуровневые функции, например, C/C++, это может привести к расхождениям в том, что считается концом имени файла: exploit.asp;.jpg или exploit.asp%00.jpg

* Попробуйте использовать многобайтовые символы Юникода, которые могут быть преобразованы в нулевые байты и точки после преобразования или нормализации Юникода. Такие последовательности, как xC0 x2E, xC4 xAE или xC0 xAE, могут быть преобразованы в x2E, если имя файла проанализировано как строка UTF-8, но затем преобразовано в символы ASCII перед использованием в пути.

Другие средства защиты включают удаление или замену опасных расширений, чтобы предотвратить выполнение файла. Если это преобразование не применяется рекурсивно, вы можете расположить запрещенную строку таким образом, чтобы после ее удаления оставалось допустимое расширение файла. Например:
```
exploit.p.phphp
```

## Writeup

* Имеющиеся данные: 
    * Данные нашего аккаунта: wiener:peter

Главная страница:

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_obfuscated_file_extension/assets/1.png)

Логинимся c имеющимися данными. У нас есть возможность загрузки аватара пользователя. Загрузим экплоит. Мы получаем сообщение о том, что файл с данным расширением недоступен для загрузки.

Код эксплоита:
```
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_obfuscated_file_extension/assets/2.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_obfuscated_file_extension/assets/3.png)

Попробуем снова загрузить наш экплоит. Перехватим запрос. Изменим название файла на **exploit.php%00.jpg**, добавив нулевой байт.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_obfuscated_file_extension/assets/4.png)

Отправляем запрос и получаем сообщение об успешной загрузке файла.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_obfuscated_file_extension/assets/5.png)

В строке поиска откроем наш файл, убрав нулевой байт. Получаем содержимое необходимого файла.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_obfuscated_file_extension/assets/6.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_obfuscated_file_extension/assets/7.png)

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_obfuscated_file_extension/assets/8.png)

Подтверждаем ответ и получаем ответ от сервера.

![](https://github.com/fobblified/Writeups/blob/main/Portswigger/File_upload_vulnerabilities/Web_shell_upload_via_obfuscated_file_extension/assets/9.png)
