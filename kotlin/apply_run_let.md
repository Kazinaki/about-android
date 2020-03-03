- [Назад](/./kotlin.md)

## apply, run, let,  with, also

| Функция | Сигнатура| Что возвращает | Обращение в лямбде к объекту получателю по **it** | Ограничивает область видимости |
| :-: | :-: | :-: | :-: | :-: |
| **apply** | inline fun T.apply(block: T.() -> Unit): T | объект — получатель | - | + |
| **run** | inline fun <T, R> T.run(block: T.() -> R): R | результат лямбды | - | + |
| **let** | inline fun <T, R> T.let(block: (T) -> R): R | результат лямбды | + | - |
| **with** | inline fun <T, R> with(receiver: T, block: T.() -> R): R | результат лямбды | - | + |
| **also** | inline fun <T> T.also(block: (T) -> Unit): T | объект — получатель | + | - |

### apply

Настраивает переданный объект, возвращает его же

```kotlin
val cat = Cat().apply {
    name = "Vasya"
    age = 2
}
```

### run

Как и **apply** позволяет в лямбде делать вызовы функций объекта, но возращает результат лямбды. Это позволяет делать цепочки вызовов.

```kotlin
fun createCat(name: String) : Cat {...}

fun washCat(cat: Cat) {...}

"Vasya"
    .run(::createCat)
    .run(::washCat)
```

### let

Создает область видимости, в которой можно ссылаться на переданный объект через **it**, возвращает результат последней строки лямбды. Часто используется с проверкой на **null**.

```kotlin
cat?.let {
    Log.d("log", "cat exist")
}
```

### also

Как и **let**, но возвращает объект. Что тоже позволяет делать цепочки вызовов.

```kotlin
cat?.also {
    washCat(it)
}?.also {
    feedCat(it)
}
```

### with

Почти как **run**, но объект получатель должен передаваться ей в качестве аргумента. Тоже возвращает результат лямбды.

```kotlin
val catNameAndAge = with(cat) {
    "name: $it.name. age $it.age"
}
```
