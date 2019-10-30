Тут заметки про Java
-----------
- [Сериализация](#Сериализация)
- [Integer и Int](#Integer-и-Int)
- [Примитивные типы](#Примитивные-типы)

## Сериализация
Сериализация -  процесс перевода какой-либо структуры данных в последовательность битов. Используется для хранения и передачи данных.

### Потоки

   Каждая программа должна записывать свои данные в место хранения или канал, и каждая программа должна считывать данные из канала или места хранения. В Java, эти каналы, куда программы записывают и откуда программы считывают данные, называются Потоками(Stream).
![java01.jpg](/img/java01.jpg)

   Потоки в основном делятся на два типа:
- Байтовые классы-потоки именуемые **Streams**
- Символьные классы-потоки именуемые **Reader** и **Writer**

Каждый поток записи данных, содержит набор методов записи. И каждый поток считывания данных, соответственно имеет подобный набор методов чтения. Как только поток создается, все эти методы должны быть вызваны.

  ### Персистентность

Персистентность объекта это способность обекта жить или по-другому — «пережить» выполнение программы. Это значит, что любой объект, который был создан во времени выполнения, уничтожается «мусорщиком» JVM, всякий раз, когда данный объект далее перестает использоваться. Но в случае реализации API персистентности, данные объекты не будут уничтожаться «мусорщиком» JVM, вместо чего им будет позволено «жить», что также дает возможность доступа к ним при следующем запуске приложения. Другими словами, персистенция означает существование времени жизни объекта, независимо от времени жизни приложения, которое запущено.

   Один из способов реализации персистентности это хранение объектов где-нибудь во внешнем файле или в базе данных, а затем восстановление их в более позднее время, используя данные файлы или базу данных как источники. Здесь сериализация и вступает в игру. Любой неперсистентный объект существует так долго, как долго работает JVM.

   Сериализованные объекты – это просто объекты, преобразованные в потоки, которые затем сохраняются во внешний файл или передаются через сеть для хранения и восстановления.


### Реализация интерфейса Serializable

Класс должен реализовывать интерфейс **java.io.Serializable**. Сам интерфейс **Serializable** не имеет никаких методов, просто идентифицирует объект как сериализируемый. Когда сериализуется объект, сохраняются только его поля, а не методы. Собственно, сама сериализация реализуется при помощи объектов **ObjectInputStream** и **ObjectOutputStream**. Вот код:
```java
public class SerialTesting {
    static class TestSerial implements Serializable {
        public int someField;
    }
 
    static void SerWrite() throws IOException {
        FileOutputStream fos = new FileOutputStream("temp.out");
        ObjectOutputStream oos = new ObjectOutputStream(fos);
        TestSerial ts = new TestSerial();
        ts.someField = 30;
        oos.writeObject(ts);
        oos.close();
    }
 
    static void SerRead() throws IOException, ClassNotFoundException {
        FileInputStream fis = new FileInputStream("temp.out");
        ObjectInputStream oin = new ObjectInputStream(fis);
        TestSerial ts = (TestSerial) oin.readObject();
        System.out.println(ts.someField);
    }
 
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        SerWrite();
        SerRead();
    }
}
```
   Для записи сериализованного объекта создаем объект **ObjectOutputStream**, в качестве аргумента в контрукторе он принимает объект класса **OutputStream** (в данном случае это **FileOutputStream**, который является потомком **OutputStream**). Ну а дальше методом **writeObject()**, объект сериализуется и посылается в **FileInputStream**.

   Для чтения создается объект **ObjectInputStream**, который принимает аргументов объект FileInputStream. Ну и читаем объект методом **readObject** кастуя его в **TestSerial**.

   ### Дополнение

   Если у сериализуемого объекта есть поле объектного типа, причем этот объект не реализует интерфейс **Serializable**, то если это поле заполнено, получим ошибку при выполнении программы, если же поле не заполнено, то ошибки не будет.

[Сериализация как она есть](http://www.skipy.ru/technics/serialization.html)

[Как работает сериализация](http://info.javarush.ru/translation/2013/09/03/%D0%9A%D0%B0%D0%BA-%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D0%B0%D0%B5%D1%82-%D1%81%D0%B5%D1%80%D0%B8%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F-%D0%B2-Java.html)

[Сериализация в JAVA](https://habrahabr.ru/post/60317/)

## Integer и Int
### Интересные факты о сравнении объектов Integer

   В исходниках Integer.java можно найти такие строки:

   ```java
   /**
  * Cache to support the object identity semantics of autoboxing for values between
  * -128 and 127 (inclusive) as required by JLS.
  *
  * The cache is initialized on first usage.  The size of the cache
  * may be controlled by the {@code -XX:AutoBoxCacheMax=<size>} option.
  * During VM initialization, java.lang.Integer.IntegerCache.high property
  * may be set and saved in the private system properties in the
  * sun.misc.VM class.
  */
 
 private static class IntegerCache {
     static final int low = -128;
     static final int high;
     static final Integer cache[];
 
     static {
         // high value may be configured by property
         int h = 127;
         ......
         cache = new Integer[(high - low) + 1];
         int j = low;
         for(int k = 0; k < cache.length; k++)
             cache[k] = new Integer(j++);
         ......
 }
 
 public static Integer valueOf(int i) {
     if (i >= IntegerCache.low && i <= IntegerCache.high)
         return IntegerCache.cache[i + (-IntegerCache.low)];
     return new Integer(i);
 }
   ```
Собственно в комментариях все написано: значения от -128 до 127 кэшируются в памяти, и при создании объекта Integer в этом периоде, фактически не создается новый объект, а возвращается ссылка на объект кэша. Отсюда получается такое:
```java
Integer a = 50;
Integer b = 50;
System.out.println(a==b); //true
 
Integer c = 500;
Integer d = 500;
System.out.println(c==d); //false
 
//однако если использовать принудительное создание объекта методом new
Integer x = new Integer(50);
Integer y = new Integer(50);
System.out.println(x==y); //false
```
Еще немного кода
```java
Integer a = 10;
int d = 10;
byte by = 10;
short sh = 10;
Short SH = 10;
Integer x = 10;
Integer b = new Integer(10);
Integer c = Integer.valueOf(10);
Long al = Long.valueOf(10);
 
System.out.println(a == b); //false - a и b ссылки на разные объекты
System.out.println(a == c); //true - a и c ссылки на один объект
System.out.println(a.equals(b)); //true - значения a и b равны
System.out.println(a.equals(c)); //true - значения a и c равны
System.out.println(a.equals(al)); //false - значения a и al равны, но типы объектов разные
System.out.println(b.equals(al)); //false - значения a и al равны, но типы объектов разные
System.out.println(d == al); //true - тут сравниваются значения, т.к. d - простой тип, а не ссылка
```
## Примитивные типы

В Java есть 8 примитивных типов:

- **byte**, **short**, **int**, **long** - целые числа
- **float**, **double** - числа с плавающей запятой
- **char** - символы
- **boolean** - булево

### Целые числа

| Наименование | Длина в байтах | Диапазон допустимых значений                             |
| ------------ | -------------- | -------------------------------------------------------- |
| long         | 64             | -9 223 372 036 854 775 808 ... 9 223 372 036 854 775 807 |
| int          | 32             | -2 147 483 648 ... 2 147 483 647                         |
| short        | 16             | -32 768 ... 32 767                                       |
| byte         | 8              | -128 ... 127                                             |

  **byte**

  Удобны для работы с потоками ввода-вывода данных в сети или файлах, также для работы с необработанными двоичными данными.

  **short**

  Применяется редко.

  **int**

  Употребляется наиболее часто. Используются для управления циклами и индексирования массивов. При указании значений byte или short в  выражениях их значения продвигаются к типу int, поэтому int используют  чаще.

  **long**

  Для больших чисел, типа время в миллисекундах и т.д.

### Числа с плавающей точкой

| Наименование | Длина в байтах | Приблизительный диапазон допустимых значенийПриблизительный диапазон допустимых значений |
| ------------ | -------------- | ------------------------------------------------------------ |
| double       | 64             | 4.9е^(-324) ... 1.8е^308                                     |
| float        | 32             | 1.4e^(-045) ... 3.4e^038                                     |

  **float**

  Применяется там где не нужна большая точность после зяпятой, например для хранения сумм в рублях и копейках.

  **double**

  Для хранения чисел с большой точностью после запятой: синусы, косинусы, корни из числа.

### Символы

  Значение char хранится в кодировке Unicode (определяет полный  набор международных символов на всех известных языках мира) и занимает  16 бит. Диапазон возможных значений от 0 до 65536. Стандартный набор  символов, известный как код ASCII, содержит значения от 0 до 127, а  расширенный 8-ми разрядный набор ISO-Latin-1 - значения от 0 до 255.

  Формально значение char он содержит число, значит к нему можно применять арифметические операции. 