Тут заметки про Kotlin
-----------
- [Unit, Nothing, Any](/kotlin/unit_nothing.md)
- [apply, run, let, with, also](/kotlin/apply_run_let.md)
- [Effective kotlin кратко. Иммутабельность](/kotlin/effective_kotlin_1.md)
- [Effective kotlin кратко. null](/kotlin/effective_kotlin_2.md)

Модификаторы видимости

Что такое Unit? Для чего он нужен?
 Зачем нужен тип Nothing, какие выражения имеют такой тип? Какие ограничения накладываются на Nothing?
 Когда компилятор использует примитивные типы? В каких случаях происходит автобоксинг?

Какие объекты выделяются при вызове forEach, map, reduce, fold и т. п. на Iterable? на Sequence?
 Зачем вообще нужны Sequences?
 Что такое функциональный тип, какие у него ограничения?
 Что использовать вместо try-with-resources?

Как компилируются object declarations и companion objects?
 Scope functions. В каких случаях стоит использовать with, apply, also, run, let

Какая польза от typealias? Во что он компилируется?
 Как работают SAM-conversions?

Что такое функции высшего порядка (higher-order functions), лямбда-выражения (lambda-expressions), анонимные функции (anonymous functions), указатели на методы (method references, bound callable references)?
 Какой толк от inline-функций, как компилируется сама функция и места её использования?
 Как работают crossinline и noinline? 
 Можно ли в лямбде изменить значение переменной, захваченной из внешней области видимости? Как это работает в inline-функциях? noinline-функциях? во многопоточном окружении?
 Как компилируются default-методы в интерфейсах?

Как компилируются extension-функции и как использовать их из Java?
 Можно ли сделать extension к интерфейсу? Тайпалиасу? Функциональному типу?
 Может ли функция быть одновременно member и extension? Как её использовать?

Почему присваивания — не выражения (assignments are not expressions)?

Что такое exhaustive when, на каких типах он возможен?

Чем дженерики в Kotlin отличаются от джавовых?

Что такое declaration-site variance? Где помогает, где затрудняет разработку, как выглядит из Java?
 Как устроен reified, почему работает только в inline-функциях?

Как с помощью reified получить не сырой тип (класс), а параметризованный тип (parameterized type) со всеми его аргументами (actual type arguments)?
