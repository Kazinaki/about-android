- [Назад](/./kotlin.md)

## Иммутабельные свойства

Лучше делать иммутабельные свойства (val). Однако надо помнить, что это не значит что они совсем неизменяемы, т.к.
1. Если в свойстве val коллекция, то ее можно изменить. Чтобы избежать этого надо использовать немутабельные коллекции.
2. Если в свойстве val определен геттер, то он может выдавать разные значения, т.к. значение свойства вычисляется каждый раз при обращении к геттеру
``` kotlin
    var name: String = "Ivan"
    var surname: String = "Petrov"
    val fullname
        get() = "$name $surname"
        
    fun main() {
        println(fullname) // Ivan Petrov
        name = "Oleg"
        println(fullname) // Oleg Petrov
    }
```

Тут еще стоит упомянуть, что если поле val вычисляемое, но без геттера, то оно вычисляется один раз - при создании объекта. И можно использовать SmartCast, например при проверке на null:
``` kotlin
    var name: String? = "Ivan"
    var surname: String = "Petrov"
    val fullname: String? = "$name $surname"
    val fullnameGetter: String?
        get() = "$name $surname"
        
    fun main() {
        if (fullname != null) {
            println(fullname.lenght)
        }
        
        if (fullnameGetter != null) {
            println(fullnameGetter.lenght) // Error: Smart cast impossible
        }
    }
```

## Иммутабельные и мутабельные коллекции Kotlin

![Коллекции Kotlin](/img/kotlin_collections.png)
На рисунке слева иммутабельные коллекции Kotlin, справа мутабельные, снизу классы JVM. Каждый мутабельный интерфейс наследует соответствующий иммутабельный, добавляя методы изменения. Read-only коллекции необязательно иммутабельные, часто они мутабельные просто спрятаны за иммутабельными интерфейсами. Например функция Iterable<T>.map() возращает объект ArrayList, который мутабельный, но возвращает его как тип List, который иммутабельный.
``` kotlin
inline fun <T, R> Iterable<T>.map(
    transformation: (T) -> R
): List<R> {
    val list = ArrayList<R>()
    for (elem in this) {
        list.add(transformation(elem))
    }
    return list
}
``` 
Таким образом его можно скастить к мутабельной коллекции. Но так делать не надо!
Вот пример кода
``` kotlin
val list = listOf(1,2,3)

// НЕ ДЕЛАЙ ТАК!
if (list is MutableList) {
    list.add(4)
}
```
Результат этого кода платформо-зависим. Например на JVM listOf() - возвращает Arrays.ArrayList, который реализует интерфейс Java.List, который имеет методы типа add()/set(), поэтому Котлин его транслирует в интерфейс Kotlin.MuttableList. Однако Arrays.ArrayList не имеет реализации этих методов. И этот код хоть и скомпилируется, но упадет в рантайме с ошибкой:

>Exception in thread “main” java.lang.UnsupportedOperationException
>
>at java.util.AbstractList.add(AbstractList.java:148)
>
>at java.util.AbstractList.add(AbstractList.java:108)

Не надо кастить иммутабельные коллекции в мутабельные, вместо этого надо использовать методы преобразования коллекций, которые возвращают другой объект.
``` kotlin
val list = listOf(1, 2, 3)

val mutableList = list.toMutableList()
mutableList.add(4)
```

Преимущества иммутабельных объектов:
1. Работать с ними проще, так они сохраняют свое состояние с момента создания.
2. Нет конфликтов при многопоточном программировании.
3. Ссылки на них могут быть закэшированы, т.к. объекты не изменяются.
4. Не надо делать защитные копии объектов.
5. Можно использовать их как ключи в мапах.

Для изменения иммутабльных объектов можно делать в них функции изменения, которые возвращают новый объект. В Kotlin это можно делать проще, используя data классы и функцию copy


