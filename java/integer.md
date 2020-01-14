- [Назад](/./java.md)

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