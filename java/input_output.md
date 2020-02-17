- [Назад](/./java.md)

## Потоки ввода-вывода

+ [Потоки байтов](#BytesStream)
+ [Абстрактные классы Reader и Writer](#AbstractReadeWriter)
+ [Чтение и запись двоичных файлов. FileInputStream и FileOutputStream](#ByteFileReadWrite)
+ [Чтение и запись массива байтов](#ByteArrayReadWrite)
+ [Буферизованные потоки BufferedInputStream и BufferedOutputStream](#BufferedWriteRead)

Объект, из которого можно считать данные, называется потоком ввода, а объект, в который можно записывать данные, - потоком вывода. Например, если надо считать содержание файла, то применяется поток ввода, а если надо записать в файл - то поток вывода.

В основе всех классов, управляющих потоками байтов, находятся два абстрактных класса: `InputStream` (представляющий потоки ввода) и `OutputStream` (представляющий потоки вывода)

Для работы с потоками символов были добавлены абстрактные классы `Reader` (для чтения потоков символов) и `Writer` (для записи потоков символов).

Все остальные классы, работающие с потоками, являются наследниками этих абстрактных классов. Основные классы потоков:

| InputStream | OutputStream | Reader | Writer |
|-|-|-|-|
| FileInputStream | FileOutputStream | FileReader | FileWriter |
| BufferedInputStream | BufferedOutputStream | BufferedReader | BufferedWriter |
| ByteArrayInputStream | ByteArrayOutputStream | CharArrayReader | CharArrayWriter |
| FileInputStream | FileOutputStream | FileReader | FileWriter |
| DataInputStream | DataOutputStream | | |
| ObjectInputStream | ObjectOutputStream | | |

### <a name="BytesStream"></a> Потоки байтов

Можно читать/писать байты по-одному, можно массивами (как сразу в/из массив, так и частично по заданному начальному смещению и количеству байт).

**Класс InputStream**

Класс `InputStream` является базовым для всех классов, управляющих байтовыми потоками ввода. Рассмотрим его основные методы:

`int available()`: возвращает количество байтов, доступных для чтения в потоке

`void close()`: закрывает поток

`int read()`: возвращает целочисленное представление следующего байта в потоке. Когда в потоке не останется доступных для чтения байтов, данный метод возвратит число -1

`int read(byte[] buffer)`: считывает байты из потока в массив buffer. После чтения возвращает число считанных байтов. Если ни одного байта не было считано, то возвращается число -1

`int read(byte[] buffer, int offset, int length)`: считывает некоторое количество байтов, равное `length`, из потока в массив `buffer`. При этом считанные байты помещаются в массиве, начиная со смещения `offset`, то есть с элемента `buffer[offset]`. Метод возвращает число успешно прочитанных байтов.

`long skip(long number)`: пропускает в потоке при чтении некоторое количество байт, которое равно `number`

**Класс OutputStream**

Класс `OutputStream` является базовым классом для всех классов, которые работают с бинарными потоками записи. Свою функциональность он реализует через следующие методы:

`void close()`: закрывает поток

`void flush()`: очищает буфер вывода, записывая все его содержимое

`void write(int b)`: записывает в выходной поток один байт, который представлен целочисленным параметром `b`

`void write(byte[] buffer)`: записывает в выходной поток массив байтов `buffer`.

`void write(byte[] buffer, int offset, int length)`: записывает в выходной поток некоторое число байтов, равное `length`, из массива `buffer`, начиная со смещения `offset`, то есть с элемента `buffer[offset]`.


### <a name="AbstractReadeWriter"></a> Абстрактные классы Reader и Writer

Методы практически идентичны, только работа не с `byte`, а с `char` и `String`.

Абстрактный класс **Reader** предоставляет функционал для чтения текстовой информации. Рассмотрим его основные методы:

`absract void close()`: закрывает поток ввода

`int read()`: возвращает целочисленное представление следующего символа в потоке. Если таких символов нет, и достигнут конец файла, то возвращается число -1

`int read(char[] buffer)`: считывает в массив `buffer` из потока символы, количество которых равно длине массива `buffer`. Возвращает количество успешно считанных символов. При достижении конца файла возвращает -1

`int read(CharBuffer buffer)`: считывает в объект `CharBuffer` из потока символы. Возвращает количество успешно считанных символов. При достижении конца файла возвращает -1

`absract int read(char[] buffer, int offset, int count)`: считывает в массив `buffer`, начиная со смещения `offset`, из потока символы, количество которых равно `count`

`long skip(long count)`: пропускает количество символов, равное `count`. Возвращает число успешно пропущенных символов

Класс **Writer** определяет функционал для всех символьных потоков вывода. Его основные методы:

`Writer append(char c)`: добавляет в конец выходного потока символ `c`. Возвращает объект `Writer`.

`Writer append(CharSequence chars)`: добавляет в конец выходного потока набор символов chars. Возвращает объект `Writer`.

`abstract void close()`: закрывает поток

`abstract void flush()`: очищает буферы потока

`void write(int c)`: записывает в поток один символ, который имеет целочисленное представление

`void write(char[] buffer)`: записывает в поток массив символов

`absract void write(char[] buffer, int off, int len)` : записывает в поток только несколько символов из массива `buffer`. Причем количество символов равно `len`, а отбор символов из массива начинается с индекса `off`

`void write(String str)`: записывает в поток строку

`void write(String str, int off, int len)`: записывает в поток из строки некоторое количество символов, которое равно `len`, причем отбор символов из строки начинается с индекса `off`

Функционал, описанный классами `Reader` и `Writer`, наследуется непосредственно классами символьных потоков, в частности классами `FileReader` и `FileWriter` соответственно, предназначенными для работы с текстовыми файлами.

### <a name="ByteFileReadWrite"></a> Чтение и запись двоичных файлов. FileInputStream и FileOutputStream

**FileOutputStream** - для записи в файл. Создаем конструкторами:

```java
FileOutputStream(String filePath)
FileOutputStream(File fileObj)
FileOutputStream(String filePath, boolean append)
FileOutputStream(File fileObj, boolean append)
```

Параметр `append` отвечает за то дописывать поток в конец файла или переписывать файл. В конструкторах без него, он принимается за `true`.

Запись строки в файл:
```java
public void WriteFoFile() {
    String text = "Hello world!"; // строка для записи
    try(FileOutputStream fos = new FileOutputStream("C://SomeDir//notes.txt"))
    {
        // перевод строки в байты
        byte[] buffer = text.getBytes();
        fos.write(buffer, 0, buffer.length);
    }
    catch(IOException ex){
        System.out.println(ex.getMessage());
    }
}
```

**FileInputStream** - для чтения из файла. Частый конструктор:

```java
FileInputStream(String fileName) throws FileNotFoundException
```

Классы `FileOutputStream` и `FileInputStream` предназначенны, прежде всего, для записи двоичных файлов. Для текстовых файлов больше подходят наследники `Reader` и `Writer`, например `FileReader` и `FileWriter`.

### <a name="ByteArrayReadWrite"></a> Чтение и запись массива байтов

Для работы с массивами байтов используются классы `ByteArrayInputStream` и `ByteArrayOutputStream`.

В отличие от остальных, после работы с ними, их не надо закрывать методом `close()`

**ByteArrayInputStream** - входной поток, использующий в качестве источника данных массив байтов. Он имеет следующие конструкторы:

```java
ByteArrayInputStream(byte[] buf) 
ByteArrayInputStream(byte[] buf, int offset, int length) 
```

**ByteArrayOutputStream** - поток вывода, использующий массив байтов в качестве места вывода.

Конструкторы:

```java
ByteArrayOutputStream() 
ByteArrayOutputStream(int size)
```

Первая версия создает массив для хранения байтов длиной в 32 байта, а вторая версия создает массив длиной size.

### <a name="BufferedWriteRead"></a> Буферизованные потоки BufferedInputStream и BufferedOutputStream

Буфферизируемые потоки используются для оптимизации операций ввода-вывода. Они добавляют к стандартным потокам специальный буфер в памяти, с помощью которого повышается производительность при чтении и записи потоков.

**BufferedInputStream** - накапливает вводимые данные в специальном буфере без постоянного обращения к устройству ввода. 

Конструкторы:

```java
BufferedInputStream(InputStream inputStream)
BufferedInputStream(InputStream inputStream, int bufSize)
```

Т.е. он сам не считывает данные, он принимает поток ввода и буфферизирует его

**BufferedOutputStream** аналогично создает буфер для потоков вывода. Этот буфер накапливает выводимые байты без постоянного обращения к устройству. И когда буфер заполнен, производится запись данных.

Конструкторы:

```java
BufferedOutputStream(OutputStream outputStream)
BufferedOutputStream(OutputStream outputStream, int bufSize)
```
