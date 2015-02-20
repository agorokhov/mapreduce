mapreduce
=========

Книжка Тома Вайта и реквизиты кластера - в почте. 

## Первые шаги на кластере

1. Зайдите на сервер bds01 по ssh. Из Linux/MaxOS - через терминал, из Widows - c помощью PuTTY.
 
	**NB:** Если вы зашли на bds01, то строка приглашения для команд будет начинаться с:
	`s09XXX@bds01` (вместо XXX - цифры вашего логина).
	Обращайте на это внимание, вся работа с кластером возможна только с bds01.


2. Установите переменную окружения `JAVA_HOME`. Можно просто скопировать мой `.profile`:
    
	`cp /home/gorokhov/.profile ~/`

    и зайти заново.

5. Скопируйте простую программу UserCount в свою директорию:
	`cp -r /home/gorokhov/usercount ~/`

	и соберите её:

	`cd ~/usercount`
	
	`ant`

1. Запустите программу:
	`hadoop jar jar/UserCount.jar ru.mipt.UserCount /data/user_events /user/<YOUR_LOGIN>/out`

1. После этого в директории `/user/<YOUR_LOGIN>/out` на кластере появится результат её работы. Его можно посмотреть командами hdfs.

	Список файлов:

	`hadoop fs -ls /user/<YOUR_LOGIN>/out`

	Прочитать 20 строк файла:

	`hadoop fs -cat hadoop fs -cat /user/<YOUR_LOGIN>/out/part-r-00000 | head -20`

	Скачать файл на сервер:

	`hadoop fs -get /user/<YOUR_LOGIN>/out/part-r-00000 .`


## Копирование файлов на сервер и обратно

Сервер bds01 доступен по ssh. Поэтому для получения с него файлов к себе на компьютер делаем так: 

Linux:

`$ scp -P <PORT> <YOUR_LOGIN>@<HOST>:<REMOTE_PATH> <LOCAL_PATH>`

Windows: 
`$ pscp -P <PORT> <YOUR_LOGIN>@<HOST>:<REMOTE_PATH> <LOCAL_PATH>`

Для отправки файлов на сервер:

Linux:

`$ scp -P <PORT> <LOCAL_PATH> <YOUR_LOGIN>@<HOST>:<REMOTE_PATH>`

Windows: 
`$ pscp -P <PORT> <LOCAL_PATH> <YOUR_LOGIN>@<HOST>:<REMOTE_PATH>`

Не забывайте еще ключ `-r` если копируете директорию.
Прорамма `pscp` под Windows идет вместе с PuTTY, поэтому чтобы она была доступна, надо прописать путь к ней в переменной окружения `PATH`.

Другой способ для Windows - программа WinSCP. При подключении указывайте протокол передачи SCP и номер порта должен быть не 22, а пятизначный, как в реквизитах кластера.

## Среда разработки
### Самый простой способ
Взять `vim` и писать код прямо на сервере. Разрешаю взять мой простой `.vimrc` =)

Если вы хотите красивую среду с GUI, для вас следующие разделы.

### Linux

Можно писать задачи прямо на кластере в Intellij IDEA (или Eclipse, кому как удобно). 

На Ubuntu при подключении по ssh нужно подключаться так:

    $ ssh <YOUR_LOGIN>@<HOST> -X -C
    
(скорее всего при первом подключении с -X будет написано, что не удалось - переподключитесь).

IDEA уже есть на сервере тут: `/usr/local/idea-IC-139.225.3`
Можно сделать символьлую ссылку в удобную для себя директорию (на сервере):

    $ ls -s /usr/local/idea-IC-139.225.3 ~/IDEA

После этого можно запускать IDEA так:

    $ ~/IDEA/bin/idea.sh
    
при этом окошко будет пробрасываться вам, хотя IDEA будет работать на удаленной машине. 
То есть, запуская с `-X`вы говорите, что окошки надо отображать у вас на дисплее, а `-C` отвечает за сжатие (можно и без него).


### Windows

## Тестирование программы
### Локальный запуск
### Запуск на кластере


## Сдача задания

При сдаче задания просьба указывать:

1. Логин и номер задачи
2. Директорию на сервере с исходниками
3. Как их собирать
4. Как запускать, т.е. какие аргументы у задачи
5. Результат работы программы - в HDFS.

## Типичные ошибки и недочеты

### MapReduce 

1. У Hadoop есть 2 Java API: старый и новый. О различиях есть в книжке Вайта и тут: [http://ruhadoop.blogspot.ru/2012/07/hadoop-api.html]([http://ruhadoop.blogspot.ru/2012/07/hadoop-api.html] "Старый и новый Hadoop API"). Можно использовать старый API (там, где это не запрещено в условии задачи), а вообще желательно новый.

1. Для глобальной сортировки надо явно указывать 1 редьюсер

2. Если на входе задачи текст, где ключ отделен от значения табом, то используйте KeyValueTextInputFormat.
	
	Если задача решается в две Job'ы и первая выводит в TextOutputFormat, то на входе второй лучше использовать KeyValueTextInputFormat. Читаем у Вайта:
 
	> **KeyValueTextInputFormat**
	> TextInputFormat’s keys, being simply the offset within the file, are not normally very useful. It is common for each line in a file to be a key-value pair, separated by a delimiter such as a tab character. For example, this is the output produced by  TextOutputFormat, Hadoop’s default  OutputFormat. To interpret such files correctly,  KeyValueTextInputFormat is appropriate.

3. Обратите внимание на стандартные `IntSumReducer, InverseMapper, ChainMapper, ChainReducer`. Найдите их в книжке, там есть еще полезные классы. Использовать их предпочтительней, чем собственные.

### Оформление кода

Ошибками на является, но стоит учесть.

1. Для отступов в коде используйте либо табы, либо пробелы. Главное - не их смесь. Желательно пробелы. Код без отступов вообще не рассматривается.
2. Импорты со звездочкой типа
	
	`import java.util.*;`
	
	не являются хорошим стилем. Лучше всегда знать, что импортируешь.  
